---
title: "替换`<machineKey>`在 ASP.NET 中"
author: rick-anderson
description: "替换`<machineKey>`在 ASP.NET 中"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: c151a48b22d6d0d6e0a8fa88a9868767d5897b2d
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="replacing-machinekey-in-aspnet"></a><span data-ttu-id="ab521-103">替换`<machineKey>`在 ASP.NET 中</span><span class="sxs-lookup"><span data-stu-id="ab521-103">Replacing `<machineKey>` in ASP.NET</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="ab521-104">实现`<machineKey>`在 ASP.NET 中的元素[是可替换](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)。</span><span class="sxs-lookup"><span data-stu-id="ab521-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="ab521-105">这允许对 ASP.NET 加密例程的大多数调用，以通过替换数据保护机制，包括新的数据保护系统路由。</span><span class="sxs-lookup"><span data-stu-id="ab521-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="ab521-106">包安装</span><span class="sxs-lookup"><span data-stu-id="ab521-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="ab521-107">新的数据保护系统只能安装到现有的 ASP.NET 应用程序面向.NET 4.5.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="ab521-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or higher.</span></span> <span data-ttu-id="ab521-108">安装将失败，如果应用程序面向.NET 4.5 或降低。</span><span class="sxs-lookup"><span data-stu-id="ab521-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="ab521-109">若要安装新的数据保护系统到现有的 ASP.NET 4.5.1+ 项目之后，安装包 Microsoft.AspNetCore.DataProtection.SystemWeb。</span><span class="sxs-lookup"><span data-stu-id="ab521-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="ab521-110">这将实例化数据保护系统使用[默认配置](xref:security/data-protection/configuration/default-settings)设置。</span><span class="sxs-lookup"><span data-stu-id="ab521-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="ab521-111">当你安装此程序包时，它将插入行到*Web.config*这是告诉 ASP.NET，以将其用于[最加密操作](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)，包括窗体身份验证、 视图状态和调用MachineKey.Protect。</span><span class="sxs-lookup"><span data-stu-id="ab521-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="ab521-112">插入的行，如下所示读取。</span><span class="sxs-lookup"><span data-stu-id="ab521-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="ab521-113">你可以判断是否处于活动状态通过检查类似的字段访问新的数据保护系统`__VIEWSTATE`，这应该以开头"CfDJ8"以下示例所示。</span><span class="sxs-lookup"><span data-stu-id="ab521-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="ab521-114">"CfDJ8"是标识受数据保护系统的负载的幻"09 F0 C9 F0"标头的 base64 表示。</span><span class="sxs-lookup"><span data-stu-id="ab521-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk..." />
```

## <a name="package-configuration"></a><span data-ttu-id="ab521-115">包配置</span><span class="sxs-lookup"><span data-stu-id="ab521-115">Package configuration</span></span>

<span data-ttu-id="ab521-116">数据保护系统是具有默认零安装程序配置实例化。</span><span class="sxs-lookup"><span data-stu-id="ab521-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="ab521-117">但是，由于默认情况下会将密钥保存到本地文件系统，这不起作用的应用程序的场中部署的状态。</span><span class="sxs-lookup"><span data-stu-id="ab521-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="ab521-118">若要解决此问题，你可以通过创建一种类型的子类 DataProtectionStartup 提供配置，并重写其 ConfigureServices 方法。</span><span class="sxs-lookup"><span data-stu-id="ab521-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="ab521-119">下面是一个示例配置将在其中保留密钥，又如何要加密对静止的自定义的数据保护启动类型。</span><span class="sxs-lookup"><span data-stu-id="ab521-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="ab521-120">它还通过提供其自己的应用程序名称来重写默认应用程序隔离策略。</span><span class="sxs-lookup"><span data-stu-id="ab521-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="ab521-121">你还可以使用`<machineKey applicationName="my-app" ... />`代替 SetApplicationName 显式调用。</span><span class="sxs-lookup"><span data-stu-id="ab521-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="ab521-122">这是不会强制执行开发人员可以创建 DataProtectionStartup 派生类型，如果他们想要配置所有已设置应用程序名称的方便机制。</span><span class="sxs-lookup"><span data-stu-id="ab521-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="ab521-123">若要启用此自定义配置，请返回到 Web.config，并查找`<appSettings>`包安装到的配置文件添加的元素。</span><span class="sxs-lookup"><span data-stu-id="ab521-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="ab521-124">它将类似以下的标记：</span><span class="sxs-lookup"><span data-stu-id="ab521-124">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="ab521-125">填写的空白值与你刚刚创建的 DataProtectionStartup 派生的类型的程序集限定名称。</span><span class="sxs-lookup"><span data-stu-id="ab521-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="ab521-126">如果应用程序的名称是 DataProtectionDemo，这将如下所示下面。</span><span class="sxs-lookup"><span data-stu-id="ab521-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="ab521-127">最新配置数据保护系统现已在应用程序中使用。</span><span class="sxs-lookup"><span data-stu-id="ab521-127">The newly-configured data protection system is now ready for use inside the application.</span></span>