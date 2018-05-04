---
title: 使用 ASP.NET Core 构建 Web API
author: scottaddie
description: 了解用于在 ASP.NET Core 中构建 Web API 的功能以及每项功能的适用场景。
manager: wpickett
ms.author: scaddie
ms.custom: mvc
ms.date: 04/24/2018
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: web-api/index
ms.openlocfilehash: 017bcc1ed65b1baa92408db07201d1c7bab2849d
ms.sourcegitcommit: 01db73f2f7ac22b11ea48a947131d6176b0fe9ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2018
---
# <a name="build-web-apis-with-aspnet-core"></a><span data-ttu-id="d04a3-103">使用 ASP.NET Core 构建 Web API</span><span class="sxs-lookup"><span data-stu-id="d04a3-103">Build web APIs with ASP.NET Core</span></span>

<span data-ttu-id="d04a3-104">作者：[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="d04a3-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="d04a3-105">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/define-controller/samples)（[如何下载](xref:tutorials/index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d04a3-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/define-controller/samples) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d04a3-106">本文档说明如何在 ASP.NET Core 中构建 Web API 以及每项功能的最佳适用场景。</span><span class="sxs-lookup"><span data-stu-id="d04a3-106">This document explains how to build a web API in ASP.NET Core and when it's most appropriate to use each feature.</span></span>

## <a name="derive-class-from-controllerbase"></a><span data-ttu-id="d04a3-107">从 ControllerBase 派生类</span><span class="sxs-lookup"><span data-stu-id="d04a3-107">Derive class from ControllerBase</span></span>

<span data-ttu-id="d04a3-108">从控制器（旨在用作 Web API）中的 [ControllerBase](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase) 类继承。</span><span class="sxs-lookup"><span data-stu-id="d04a3-108">Inherit from the [ControllerBase](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase) class in a controller that's intended to serve as a web API.</span></span> <span data-ttu-id="d04a3-109">例如:</span><span class="sxs-lookup"><span data-stu-id="d04a3-109">For example:</span></span>

::: moniker range=">= aspnetcore-2.1"
<span data-ttu-id="d04a3-110">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="d04a3-110">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]</span></span>
::: moniker-end
::: moniker range="<= aspnetcore-2.0"
<span data-ttu-id="d04a3-111">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="d04a3-111">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]</span></span>
::: moniker-end

<span data-ttu-id="d04a3-112">通过 `ControllerBase` 类可使用大量属性和方法。</span><span class="sxs-lookup"><span data-stu-id="d04a3-112">The `ControllerBase` class provides access to numerous properties and methods.</span></span> <span data-ttu-id="d04a3-113">前面的示例中包含某些此类方法，如 [BadRequest](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.badrequest) 和 [CreatedAtAction](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.createdataction)。</span><span class="sxs-lookup"><span data-stu-id="d04a3-113">In the preceding example, some such methods include [BadRequest](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.badrequest) and [CreatedAtAction](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.createdataction).</span></span> <span data-ttu-id="d04a3-114">将在操作方法中调用这些方法以分别返回 HTTP 400 和 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="d04a3-114">These methods are invoked within action methods to return HTTP 400 and 201 status codes, respectively.</span></span> <span data-ttu-id="d04a3-115">将使用 [ModelState](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.modelstate) 属性（还可由 `ControllerBase` 提供）执行请求模型验证。</span><span class="sxs-lookup"><span data-stu-id="d04a3-115">The [ModelState](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.modelstate) property, also provided by `ControllerBase`, is accessed to perform request model validation.</span></span>

::: moniker range=">= aspnetcore-2.1"
## <a name="annotate-class-with-apicontrollerattribute"></a><span data-ttu-id="d04a3-116">使用 ApiControllerAttribute 批注类</span><span class="sxs-lookup"><span data-stu-id="d04a3-116">Annotate class with ApiControllerAttribute</span></span>

<span data-ttu-id="d04a3-117">ASP.NET Core 2.1 引入了 `[ApiController]` 特性，用于批注 Web API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="d04a3-117">ASP.NET Core 2.1 introduces the `[ApiController]` attribute to denote a web API controller class.</span></span> <span data-ttu-id="d04a3-118">例如:</span><span class="sxs-lookup"><span data-stu-id="d04a3-118">For example:</span></span>

<span data-ttu-id="d04a3-119">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=2)]</span><span class="sxs-lookup"><span data-stu-id="d04a3-119">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=2)]</span></span>

<span data-ttu-id="d04a3-120">此特性通常与 `ControllerBase` 配合使用以获得其他有用的方法和属性。</span><span class="sxs-lookup"><span data-stu-id="d04a3-120">This attribute is commonly coupled with `ControllerBase` to gain access to useful methods and properties.</span></span> <span data-ttu-id="d04a3-121">通过 `ControllerBase` 可使用 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 和 [File](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.file) 等方法。</span><span class="sxs-lookup"><span data-stu-id="d04a3-121">`ControllerBase` provides access to methods such as [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) and [File](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.file).</span></span>

<span data-ttu-id="d04a3-122">另一种方法是创建使用 `[ApiController]` 特性进行批注的自定义基本控制器类：</span><span class="sxs-lookup"><span data-stu-id="d04a3-122">Another approach is to create a custom base controller class annotated with the `[ApiController]` attribute:</span></span>

<span data-ttu-id="d04a3-123">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/MyBaseController.cs?name=snippet_ControllerSignature)]</span><span class="sxs-lookup"><span data-stu-id="d04a3-123">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/MyBaseController.cs?name=snippet_ControllerSignature)]</span></span>

<span data-ttu-id="d04a3-124">以下各部分说明该特性添加的便利功能。</span><span class="sxs-lookup"><span data-stu-id="d04a3-124">The following sections describe convenience features added by the attribute.</span></span>

### <a name="automatic-http-400-responses"></a><span data-ttu-id="d04a3-125">自动 HTTP 400 响应</span><span class="sxs-lookup"><span data-stu-id="d04a3-125">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="d04a3-126">验证错误会自动触发 HTTP 400 响应。</span><span class="sxs-lookup"><span data-stu-id="d04a3-126">Validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="d04a3-127">操作中不需要以下代码：</span><span class="sxs-lookup"><span data-stu-id="d04a3-127">The following code becomes unnecessary in your actions:</span></span>

<span data-ttu-id="d04a3-128">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?range=46-49)]</span><span class="sxs-lookup"><span data-stu-id="d04a3-128">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?range=46-49)]</span></span>

<span data-ttu-id="d04a3-129">在 Startup.ConfigureServices 中使用以下代码将禁用此默认行为：</span><span class="sxs-lookup"><span data-stu-id="d04a3-129">This default behavior is disabled with the following code in *Startup.ConfigureServices*:</span></span>

<span data-ttu-id="d04a3-130">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]</span><span class="sxs-lookup"><span data-stu-id="d04a3-130">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]</span></span>

### <a name="binding-source-parameter-inference"></a><span data-ttu-id="d04a3-131">绑定源参数推理</span><span class="sxs-lookup"><span data-stu-id="d04a3-131">Binding source parameter inference</span></span>

<span data-ttu-id="d04a3-132">绑定源特性定义可找到操作参数值的位置。</span><span class="sxs-lookup"><span data-stu-id="d04a3-132">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="d04a3-133">存在以下绑定源特性：</span><span class="sxs-lookup"><span data-stu-id="d04a3-133">The following binding source attributes exist:</span></span>

|<span data-ttu-id="d04a3-134">特性</span><span class="sxs-lookup"><span data-stu-id="d04a3-134">Attribute</span></span>|<span data-ttu-id="d04a3-135">绑定源</span><span class="sxs-lookup"><span data-stu-id="d04a3-135">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="d04a3-136">**[[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute)**</span><span class="sxs-lookup"><span data-stu-id="d04a3-136">**[[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute)**</span></span>     | <span data-ttu-id="d04a3-137">请求正文</span><span class="sxs-lookup"><span data-stu-id="d04a3-137">Request body</span></span> |
|<span data-ttu-id="d04a3-138">**[[FromForm]](/dotnet/api/microsoft.aspnetcore.mvc.fromformattribute)**</span><span class="sxs-lookup"><span data-stu-id="d04a3-138">**[[FromForm]](/dotnet/api/microsoft.aspnetcore.mvc.fromformattribute)**</span></span>     | <span data-ttu-id="d04a3-139">请求正文中的表单数据</span><span class="sxs-lookup"><span data-stu-id="d04a3-139">Form data in the request body</span></span> |
|<span data-ttu-id="d04a3-140">**[[FromHeader]](/dotnet/api/microsoft.aspnetcore.mvc.fromheaderattribute)**</span><span class="sxs-lookup"><span data-stu-id="d04a3-140">**[[FromHeader]](/dotnet/api/microsoft.aspnetcore.mvc.fromheaderattribute)**</span></span> | <span data-ttu-id="d04a3-141">请求标头</span><span class="sxs-lookup"><span data-stu-id="d04a3-141">Request header</span></span> |
|<span data-ttu-id="d04a3-142">**[[FromQuery]](/dotnet/api/microsoft.aspnetcore.mvc.fromqueryattribute)**</span><span class="sxs-lookup"><span data-stu-id="d04a3-142">**[[FromQuery]](/dotnet/api/microsoft.aspnetcore.mvc.fromqueryattribute)**</span></span>   | <span data-ttu-id="d04a3-143">请求查询字符串参数</span><span class="sxs-lookup"><span data-stu-id="d04a3-143">Request query string parameter</span></span> |
|<span data-ttu-id="d04a3-144">**[[FromRoute]](/dotnet/api/microsoft.aspnetcore.mvc.fromrouteattribute)**</span><span class="sxs-lookup"><span data-stu-id="d04a3-144">**[[FromRoute]](/dotnet/api/microsoft.aspnetcore.mvc.fromrouteattribute)**</span></span>   | <span data-ttu-id="d04a3-145">当前请求中的路由数据</span><span class="sxs-lookup"><span data-stu-id="d04a3-145">Route data from the current request</span></span> |
|<span data-ttu-id="d04a3-146">**[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)**</span><span class="sxs-lookup"><span data-stu-id="d04a3-146">**[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)**</span></span> | <span data-ttu-id="d04a3-147">作为操作参数插入的请求服务</span><span class="sxs-lookup"><span data-stu-id="d04a3-147">The request service injected as an action parameter</span></span> |

<span data-ttu-id="d04a3-148">没有 `[ApiController]` 特性时，将显式定义绑定源特性。</span><span class="sxs-lookup"><span data-stu-id="d04a3-148">Without the `[ApiController]` attribute, binding source attributes are explicitly defined.</span></span> <span data-ttu-id="d04a3-149">在下面的示例中，`[FromQuery]` 特性指示 `discontinuedOnly` 参数值在请求 URL 的查询字符串中提供：</span><span class="sxs-lookup"><span data-stu-id="d04a3-149">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

<span data-ttu-id="d04a3-150">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=2)]</span><span class="sxs-lookup"><span data-stu-id="d04a3-150">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=2)]</span></span>

<span data-ttu-id="d04a3-151">推理规则应用于操作参数的默认数据源。</span><span class="sxs-lookup"><span data-stu-id="d04a3-151">Inference rules are applied for the default data sources of action parameters.</span></span> <span data-ttu-id="d04a3-152">这些规则将配置绑定资源，否则你可以手动应用操作参数。</span><span class="sxs-lookup"><span data-stu-id="d04a3-152">These rules configure the binding sources you're otherwise likely to manually apply to the action parameters.</span></span> <span data-ttu-id="d04a3-153">绑定源特性的行为如下：</span><span class="sxs-lookup"><span data-stu-id="d04a3-153">The binding source attributes behave as follows:</span></span>

* <span data-ttu-id="d04a3-154">**[FromBody]**，针对复杂类型参数进行推断。</span><span class="sxs-lookup"><span data-stu-id="d04a3-154">**[FromBody]** is inferred for complex type parameters.</span></span> <span data-ttu-id="d04a3-155">此规则不适用于具有特殊含义的任何复杂的内置类型，如 [IFormCollection](/dotnet/api/microsoft.aspnetcore.http.iformcollection) 和 [CancellationToken](/dotnet/api/system.threading.cancellationtoken)。</span><span class="sxs-lookup"><span data-stu-id="d04a3-155">An exception to this rule is any complex, built-in type with a special meaning, such as [IFormCollection](/dotnet/api/microsoft.aspnetcore.http.iformcollection) and [CancellationToken](/dotnet/api/system.threading.cancellationtoken).</span></span> <span data-ttu-id="d04a3-156">绑定源推理代码将忽略这些特殊类型。</span><span class="sxs-lookup"><span data-stu-id="d04a3-156">The binding source inference code ignores those special types.</span></span> <span data-ttu-id="d04a3-157">当操作中的多个参数为显式指定（通过 `[FromBody]`）或在请求正文中作为绑定进行推断时，将会引发异常。</span><span class="sxs-lookup"><span data-stu-id="d04a3-157">When an action has more than one parameter explicitly specified (via `[FromBody]`) or inferred as bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="d04a3-158">例如，下面的操作签名会导致异常：</span><span class="sxs-lookup"><span data-stu-id="d04a3-158">For example, the following action signatures cause an exception:</span></span>

<span data-ttu-id="d04a3-159">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/TestController.cs?name=snippet_ActionsCausingExceptions)]</span><span class="sxs-lookup"><span data-stu-id="d04a3-159">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/TestController.cs?name=snippet_ActionsCausingExceptions)]</span></span>

* <span data-ttu-id="d04a3-160">**[FromForm]**，针对 [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) 和 [IFormFileCollection](/dotnet/api/microsoft.aspnetcore.http.iformfilecollection) 类型的操作参数进行推断。</span><span class="sxs-lookup"><span data-stu-id="d04a3-160">**[FromForm]** is inferred for action parameters of type [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) and [IFormFileCollection](/dotnet/api/microsoft.aspnetcore.http.iformfilecollection).</span></span> <span data-ttu-id="d04a3-161">该特性不针对任何简单类型或用户定义类型进行推断。</span><span class="sxs-lookup"><span data-stu-id="d04a3-161">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="d04a3-162">**[FromRoute]**，针对与路由模板中的参数相匹配的任何操作参数名称进行推断。</span><span class="sxs-lookup"><span data-stu-id="d04a3-162">**[FromRoute]** is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="d04a3-163">当多个路由与一个操作参数匹配时，任何路由值都视为 `[FromRoute]`。</span><span class="sxs-lookup"><span data-stu-id="d04a3-163">When multiple routes match an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="d04a3-164">**[FromQuery]**，针对任何其他操作参数进行推断。</span><span class="sxs-lookup"><span data-stu-id="d04a3-164">**[FromQuery]** is inferred for any other action parameters.</span></span>

<span data-ttu-id="d04a3-165">在 Startup.ConfigureServices 中使用以下代码将禁用默认推理规则：</span><span class="sxs-lookup"><span data-stu-id="d04a3-165">The default inference rules are disabled with the following code in *Startup.ConfigureServices*:</span></span>

<span data-ttu-id="d04a3-166">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=4)]</span><span class="sxs-lookup"><span data-stu-id="d04a3-166">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=4)]</span></span>

### <a name="multipartform-data-request-inference"></a><span data-ttu-id="d04a3-167">Multipart/form-data 请求推理</span><span class="sxs-lookup"><span data-stu-id="d04a3-167">Multipart/form-data request inference</span></span>

<span data-ttu-id="d04a3-168">使用 [[FromForm]](/dotnet/api/microsoft.aspnetcore.mvc.fromformattribute) 特性批注操作参数时，将推断 `multipart/form-data` 请求内容类型。</span><span class="sxs-lookup"><span data-stu-id="d04a3-168">When an action parameter is annotated with the [[FromForm]](/dotnet/api/microsoft.aspnetcore.mvc.fromformattribute) attribute, the `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="d04a3-169">在 Startup.ConfigureServices 中使用以下代码将禁用默认行为：</span><span class="sxs-lookup"><span data-stu-id="d04a3-169">The default behavior is disabled with the following code in *Startup.ConfigureServices*:</span></span>

<span data-ttu-id="d04a3-170">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="d04a3-170">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3)]</span></span>

### <a name="attribute-routing-requirement"></a><span data-ttu-id="d04a3-171">特性路由要求</span><span class="sxs-lookup"><span data-stu-id="d04a3-171">Attribute routing requirement</span></span>

<span data-ttu-id="d04a3-172">特性路由是必要条件。</span><span class="sxs-lookup"><span data-stu-id="d04a3-172">Attribute routing becomes a requirement.</span></span> <span data-ttu-id="d04a3-173">例如:</span><span class="sxs-lookup"><span data-stu-id="d04a3-173">For example:</span></span>

<span data-ttu-id="d04a3-174">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="d04a3-174">[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=1)]</span></span>

<span data-ttu-id="d04a3-175">不能通过在 [UseMvc](/dotnet/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions.usemvc#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvc_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Action_Microsoft_AspNetCore_Routing_IRouteBuilder__) 中定义的 [传统路由](xref:mvc/controllers/routing#conventional-routing)或通过 Startup.Configure 中的 [UseMvcWithDefaultRoute](/dotnet/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions.usemvcwithdefaultroute#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 访问操作。</span><span class="sxs-lookup"><span data-stu-id="d04a3-175">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined in [UseMvc](/dotnet/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions.usemvc#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvc_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Action_Microsoft_AspNetCore_Routing_IRouteBuilder__) or by [UseMvcWithDefaultRoute](/dotnet/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions.usemvcwithdefaultroute#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) in *Startup.Configure*.</span></span>
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d04a3-176">其他资源</span><span class="sxs-lookup"><span data-stu-id="d04a3-176">Additional resources</span></span>

* [<span data-ttu-id="d04a3-177">控制器操作返回类型</span><span class="sxs-lookup"><span data-stu-id="d04a3-177">Controller action return types</span></span>](xref:web-api/action-return-types)
* [<span data-ttu-id="d04a3-178">自定义格式化程序</span><span class="sxs-lookup"><span data-stu-id="d04a3-178">Custom formatters</span></span>](xref:web-api/advanced/custom-formatters)
* [<span data-ttu-id="d04a3-179">格式化响应数据</span><span class="sxs-lookup"><span data-stu-id="d04a3-179">Format response data</span></span>](xref:web-api/advanced/formatting)
* [<span data-ttu-id="d04a3-180">使用 Swagger 的帮助页</span><span class="sxs-lookup"><span data-stu-id="d04a3-180">Help pages using Swagger</span></span>](xref:tutorials/web-api-help-pages-using-swagger)
* [<span data-ttu-id="d04a3-181">路由到控制器操作</span><span class="sxs-lookup"><span data-stu-id="d04a3-181">Routing to controller actions</span></span>](xref:mvc/controllers/routing)