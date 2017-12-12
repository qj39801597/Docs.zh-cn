---
uid: signalr/overview/guide-to-the-api/mapping-users-to-connections
title: "将 SignalR 用户映射至连接 |Microsoft 文档"
author: tfitzmac
description: "本主题说明如何保留有关用户和他们的连接信息。 Patrick Fletcher 协助编写本主题。 本主题中使用的软件版本..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/30/2014
ms.topic: article
ms.assetid: f80c08b1-3f1f-432c-980c-c7b6edeb31b1
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/guide-to-the-api/mapping-users-to-connections
msc.type: authoredcontent
ms.openlocfilehash: 9b50d8805beabbc48467e20331c7593de9bc4254
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
<a name="mapping-signalr-users-to-connections"></a><span data-ttu-id="b8aef-105">将 SignalR 用户映射至连接</span><span class="sxs-lookup"><span data-stu-id="b8aef-105">Mapping SignalR Users to Connections</span></span>
====================
<span data-ttu-id="b8aef-106">通过[Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="b8aef-106">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="b8aef-107">本主题说明如何保留有关用户和他们的连接信息。</span><span class="sxs-lookup"><span data-stu-id="b8aef-107">This topic shows how to retain information about users and their connections.</span></span>
> 
> <span data-ttu-id="b8aef-108">Patrick Fletcher 协助编写本主题。</span><span class="sxs-lookup"><span data-stu-id="b8aef-108">Patrick Fletcher helped write this topic.</span></span>
> 
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="b8aef-109">本主题中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="b8aef-109">Software versions used in this topic</span></span>
> 
> 
> - [<span data-ttu-id="b8aef-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="b8aef-110">Visual Studio 2013</span></span>](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - <span data-ttu-id="b8aef-111">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="b8aef-111">.NET 4.5</span></span>
> - <span data-ttu-id="b8aef-112">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="b8aef-112">SignalR version 2</span></span>
>   
> 
> 
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="b8aef-113">本主题的早期版本</span><span class="sxs-lookup"><span data-stu-id="b8aef-113">Previous versions of this topic</span></span>
> 
> <span data-ttu-id="b8aef-114">有关 SignalR 的早期版本的信息，请参阅[SignalR 较旧版本](../older-versions/index.md)。</span><span class="sxs-lookup"><span data-stu-id="b8aef-114">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
> 
> ## <a name="questions-and-comments"></a><span data-ttu-id="b8aef-115">问题和意见</span><span class="sxs-lookup"><span data-stu-id="b8aef-115">Questions and comments</span></span>
> 
> <span data-ttu-id="b8aef-116">请留下反馈上如何喜欢本教程的方式，我们可以提高在页面底部的注释中。</span><span class="sxs-lookup"><span data-stu-id="b8aef-116">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="b8aef-117">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="b8aef-117">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="introduction"></a><span data-ttu-id="b8aef-118">介绍</span><span class="sxs-lookup"><span data-stu-id="b8aef-118">Introduction</span></span>

<span data-ttu-id="b8aef-119">每个客户端将连接到中心将传递唯一连接 id。你可以检索中的此值`Context.ConnectionId`中心上下文的属性。</span><span class="sxs-lookup"><span data-stu-id="b8aef-119">Each client connecting to a hub passes a unique connection id. You can retrieve this value in the `Context.ConnectionId` property of the hub context.</span></span> <span data-ttu-id="b8aef-120">如果你的应用程序需要以将用户映射到的连接 id 并将保留该映射，你可以使用以下项之一：</span><span class="sxs-lookup"><span data-stu-id="b8aef-120">If your application needs to map a user to the connection id and persist that mapping, you can use one of the following:</span></span>

- [<span data-ttu-id="b8aef-121">用户 ID 提供程序 (SignalR 2)</span><span class="sxs-lookup"><span data-stu-id="b8aef-121">The User ID Provider (SignalR 2)</span></span>](#IUserIdProvider)
- <span data-ttu-id="b8aef-122">[内存中存储](#inmemory)，如字典</span><span class="sxs-lookup"><span data-stu-id="b8aef-122">[In-memory storage](#inmemory), such as a dictionary</span></span>
- [<span data-ttu-id="b8aef-123">每个用户的的 SignalR 组</span><span class="sxs-lookup"><span data-stu-id="b8aef-123">SignalR group for each user</span></span>](#groups)
- <span data-ttu-id="b8aef-124">[永久、 外部存储](#database)，如数据库表或 Azure 表存储</span><span class="sxs-lookup"><span data-stu-id="b8aef-124">[Permanent, external storage](#database), such as a database table or Azure table storage</span></span>

<span data-ttu-id="b8aef-125">每个这些实现是此主题中所示。</span><span class="sxs-lookup"><span data-stu-id="b8aef-125">Each of these implementations is shown in this topic.</span></span> <span data-ttu-id="b8aef-126">你使用`OnConnected`， `OnDisconnected`，和`OnReconnected`方法`Hub`类来跟踪用户连接状态。</span><span class="sxs-lookup"><span data-stu-id="b8aef-126">You use the `OnConnected`, `OnDisconnected`, and `OnReconnected` methods of the `Hub` class to track the user connection status.</span></span>

<span data-ttu-id="b8aef-127">你的应用程序的最佳方法取决于：</span><span class="sxs-lookup"><span data-stu-id="b8aef-127">The best approach for your application depends on:</span></span>

- <span data-ttu-id="b8aef-128">承载你的应用程序的 web 服务器的数目。</span><span class="sxs-lookup"><span data-stu-id="b8aef-128">The number of web servers hosting your application.</span></span>
- <span data-ttu-id="b8aef-129">是否需要获取当前连接的用户的列表。</span><span class="sxs-lookup"><span data-stu-id="b8aef-129">Whether you need to get a list of the currently connected users.</span></span>
- <span data-ttu-id="b8aef-130">是否需要应用程序或服务器重新启动时持久保存组和用户信息。</span><span class="sxs-lookup"><span data-stu-id="b8aef-130">Whether you need to persist group and user information when the application or server restarts.</span></span>
- <span data-ttu-id="b8aef-131">调用外部服务器的滞后时间是否出现问题。</span><span class="sxs-lookup"><span data-stu-id="b8aef-131">Whether the latency of calling an external server is an issue.</span></span>

<span data-ttu-id="b8aef-132">下表显示了哪种方法适用于这些注意事项。</span><span class="sxs-lookup"><span data-stu-id="b8aef-132">The following table shows which approach works for these considerations.</span></span>

|  | <span data-ttu-id="b8aef-133">多个服务器</span><span class="sxs-lookup"><span data-stu-id="b8aef-133">More than one server</span></span> | <span data-ttu-id="b8aef-134">获取当前连接的用户的列表</span><span class="sxs-lookup"><span data-stu-id="b8aef-134">Get list of currently connected users</span></span> | <span data-ttu-id="b8aef-135">将重新启动后的信息保留</span><span class="sxs-lookup"><span data-stu-id="b8aef-135">Persist information after restarts</span></span> | <span data-ttu-id="b8aef-136">获得最佳性能</span><span class="sxs-lookup"><span data-stu-id="b8aef-136">Optimal performance</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="b8aef-137">用户标识提供程序</span><span class="sxs-lookup"><span data-stu-id="b8aef-137">UserID Provider</span></span> | ![](mapping-users-to-connections/_static/image1.png) |  |  | ![](mapping-users-to-connections/_static/image2.png) |
| <span data-ttu-id="b8aef-138">内存中</span><span class="sxs-lookup"><span data-stu-id="b8aef-138">In-memory</span></span> |  | ![](mapping-users-to-connections/_static/image3.png) |  | ![](mapping-users-to-connections/_static/image4.png) |
| <span data-ttu-id="b8aef-139">单用户组</span><span class="sxs-lookup"><span data-stu-id="b8aef-139">Single-user groups</span></span> | ![](mapping-users-to-connections/_static/image5.png) |  |  | ![](mapping-users-to-connections/_static/image6.png) |
| <span data-ttu-id="b8aef-140">永久外部</span><span class="sxs-lookup"><span data-stu-id="b8aef-140">Permanent, external</span></span> | ![](mapping-users-to-connections/_static/image7.png) | ![](mapping-users-to-connections/_static/image8.png) | ![](mapping-users-to-connections/_static/image9.png) |  |

<a id="IUserIdProvider"></a>

## <a name="iuserid-provider"></a><span data-ttu-id="b8aef-141">IUserID 提供程序</span><span class="sxs-lookup"><span data-stu-id="b8aef-141">IUserID provider</span></span>

<span data-ttu-id="b8aef-142">此功能允许用户指定用户 Id 是基于通过新接口 IUserIdProvider IRequest。</span><span class="sxs-lookup"><span data-stu-id="b8aef-142">This feature allows users to specify what the userId is based on an IRequest via a new interface IUserIdProvider.</span></span>

<span data-ttu-id="b8aef-143">**IUserIdProvider**</span><span class="sxs-lookup"><span data-stu-id="b8aef-143">**The IUserIdProvider**</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

<span data-ttu-id="b8aef-144">默认情况下，将使用用户的实现`IPrincipal.Identity.Name`作为用户名。</span><span class="sxs-lookup"><span data-stu-id="b8aef-144">By default, there will be an implementation that uses the user's `IPrincipal.Identity.Name` as the user name.</span></span> <span data-ttu-id="b8aef-145">若要更改此设置，注册的实现`IUserIdProvider`与全局主机应用程序启动时：</span><span class="sxs-lookup"><span data-stu-id="b8aef-145">To change this, register your implementation of `IUserIdProvider` with the global host when your application starts:</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

<span data-ttu-id="b8aef-146">从中心中你将能够将消息发送到这些用户通过以下 API:</span><span class="sxs-lookup"><span data-stu-id="b8aef-146">From within a hub, you'll be able to send messages to these users via the following API:</span></span>

<span data-ttu-id="b8aef-147">**向特定用户发送消息**</span><span class="sxs-lookup"><span data-stu-id="b8aef-147">**Sending a message to a specific user**</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs?highlight=5)]

<a id="inmemory"></a>

## <a name="in-memory-storage"></a><span data-ttu-id="b8aef-148">内存中存储</span><span class="sxs-lookup"><span data-stu-id="b8aef-148">In-memory storage</span></span>

<span data-ttu-id="b8aef-149">下面的示例演示如何保留一个字典，其中存储在内存中的连接和用户信息。</span><span class="sxs-lookup"><span data-stu-id="b8aef-149">The following examples show how to retain connection and user information in a dictionary that is stored in memory.</span></span> <span data-ttu-id="b8aef-150">字典使用`HashSet`存储的连接 id。在任何时候用户可能具有多个连接到 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="b8aef-150">The dictionary uses a `HashSet` to store the connection id. At any time a user could have more than one connection to the SignalR application.</span></span> <span data-ttu-id="b8aef-151">例如，通过多个设备或多个浏览器选项卡连接的用户将具有多个连接 id。</span><span class="sxs-lookup"><span data-stu-id="b8aef-151">For example, a user who is connected through multiple devices or more than one browser tab would have more than one connection id.</span></span>

<span data-ttu-id="b8aef-152">如果应用程序关闭时，所有信息都将丢失，但它也将进行重新填充，如用户重新建立连接。</span><span class="sxs-lookup"><span data-stu-id="b8aef-152">If the application shuts down, all of the information is lost, but it will be re-populated as the users re-establish their connections.</span></span> <span data-ttu-id="b8aef-153">如果您的环境包括多个 web 服务器，因为每个服务器都可以单独的连接集合，则内存中存储无效。</span><span class="sxs-lookup"><span data-stu-id="b8aef-153">In-memory storage does not work if your environment includes more than one web server because each server would have a separate collection of connections.</span></span>

<span data-ttu-id="b8aef-154">第一个示例演示管理连接到的用户的映射的类。</span><span class="sxs-lookup"><span data-stu-id="b8aef-154">The first example shows a class that manages the mapping of users to connections.</span></span> <span data-ttu-id="b8aef-155">HashSet 的键将为用户的名称。</span><span class="sxs-lookup"><span data-stu-id="b8aef-155">The key for the HashSet will be the user's name.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

<span data-ttu-id="b8aef-156">下一个示例演示如何使用连接映射类从一个中心。</span><span class="sxs-lookup"><span data-stu-id="b8aef-156">The next example shows how to use the connection mapping class from a hub.</span></span> <span data-ttu-id="b8aef-157">类的实例存储在变量名`_connections`。</span><span class="sxs-lookup"><span data-stu-id="b8aef-157">The instance of the class is stored in a variable name `_connections`.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a><span data-ttu-id="b8aef-158">单用户组</span><span class="sxs-lookup"><span data-stu-id="b8aef-158">Single-user groups</span></span>

<span data-ttu-id="b8aef-159">可以为每个用户创建组，然后将消息发送到该组，当你想要访问只允许该用户。</span><span class="sxs-lookup"><span data-stu-id="b8aef-159">You can create a group for each user, and then send a message to that group when you want to reach only that user.</span></span> <span data-ttu-id="b8aef-160">每个组的名称是用户的名称。</span><span class="sxs-lookup"><span data-stu-id="b8aef-160">The name of each group is the name of the user.</span></span> <span data-ttu-id="b8aef-161">如果用户具有多个连接，则将每个连接 id 添加到用户的组。</span><span class="sxs-lookup"><span data-stu-id="b8aef-161">If a user has more than one connection, each connection id is added to the user's group.</span></span>

<span data-ttu-id="b8aef-162">你不应手动删除用户从组用户断开连接时。</span><span class="sxs-lookup"><span data-stu-id="b8aef-162">You should not manually remove the user from the group when the user disconnects.</span></span> <span data-ttu-id="b8aef-163">由 SignalR 框架自动执行此操作。</span><span class="sxs-lookup"><span data-stu-id="b8aef-163">This action is automatically performed by the SignalR framework.</span></span>

<span data-ttu-id="b8aef-164">下面的示例演示如何实现单用户组。</span><span class="sxs-lookup"><span data-stu-id="b8aef-164">The following example shows how to implement single-user groups.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a><span data-ttu-id="b8aef-165">永久、 外部存储</span><span class="sxs-lookup"><span data-stu-id="b8aef-165">Permanent, external storage</span></span>

<span data-ttu-id="b8aef-166">本主题演示如何使用数据库或 Azure 表存储来存储连接信息。</span><span class="sxs-lookup"><span data-stu-id="b8aef-166">This topic shows how to use either a database or Azure table storage for storing connection information.</span></span> <span data-ttu-id="b8aef-167">当你有多个 web 服务器，因为每个 web 服务器可以与相同的数据存储库进行交互时，此方法有效。</span><span class="sxs-lookup"><span data-stu-id="b8aef-167">This approach works when you have multiple web servers because each web server can interact with the same data repository.</span></span> <span data-ttu-id="b8aef-168">如果你的 web 服务器停止工作或应用程序重新启动，`OnDisconnected`不调用方法。</span><span class="sxs-lookup"><span data-stu-id="b8aef-168">If your web servers stop working or the application restarts, the `OnDisconnected` method is not called.</span></span> <span data-ttu-id="b8aef-169">因此，很可能你的数据存储库将具有的连接 id，将不再有效的记录。</span><span class="sxs-lookup"><span data-stu-id="b8aef-169">Therefore, it is possible that your data repository will have records for connection ids that are no longer valid.</span></span> <span data-ttu-id="b8aef-170">若要清理这些孤立的记录，你可能希望使与你的应用程序相关的时间范围之外创建任何连接。</span><span class="sxs-lookup"><span data-stu-id="b8aef-170">To clean up these orphaned records, you may wish to invalidate any connection that was created outside of a timeframe that is relevant to your application.</span></span> <span data-ttu-id="b8aef-171">此部分中的示例包括用于跟踪，创建连接时的一个值，但不是显示如何清除旧的记录，因为你可能想要执行此操作作为后台进程。</span><span class="sxs-lookup"><span data-stu-id="b8aef-171">The examples in this section include a value for tracking when the connection was created, but do not show how to clean up old records because you may want to do that as background process.</span></span>

### <a name="database"></a><span data-ttu-id="b8aef-172">数据库</span><span class="sxs-lookup"><span data-stu-id="b8aef-172">Database</span></span>

<span data-ttu-id="b8aef-173">下面的示例演示如何保留在数据库中的连接和用户信息。</span><span class="sxs-lookup"><span data-stu-id="b8aef-173">The following examples show how to retain connection and user information in a database.</span></span> <span data-ttu-id="b8aef-174">你可以使用任何数据访问技术;但是，下面的示例演示如何定义使用 Entity Framework 模型。</span><span class="sxs-lookup"><span data-stu-id="b8aef-174">You can use any data access technology; however, the example below shows how to define models using Entity Framework.</span></span> <span data-ttu-id="b8aef-175">这些实体模型对应于数据库表和字段。</span><span class="sxs-lookup"><span data-stu-id="b8aef-175">These entity models correspond to database tables and fields.</span></span> <span data-ttu-id="b8aef-176">您的数据结构无法千差万别，具体取决于你的应用程序的要求。</span><span class="sxs-lookup"><span data-stu-id="b8aef-176">Your data structure could vary considerably depending on the requirements of your application.</span></span>

<span data-ttu-id="b8aef-177">第一个示例演示如何定义可以与多个连接实体相关联的用户实体。</span><span class="sxs-lookup"><span data-stu-id="b8aef-177">The first example shows how to define a user entity that can be associated with many connection entities.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]

<span data-ttu-id="b8aef-178">然后，从中心，你可以跟踪每个连接的状态与下面显示的代码。</span><span class="sxs-lookup"><span data-stu-id="b8aef-178">Then, from the hub, you can track the state of each connection with the code shown below.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample8.cs)]

<a id="azure"></a>
### <a name="azure-table-storage"></a><span data-ttu-id="b8aef-179">Azure 表存储</span><span class="sxs-lookup"><span data-stu-id="b8aef-179">Azure table storage</span></span>

<span data-ttu-id="b8aef-180">下面的 Azure 表存储示例是类似于数据库的示例。</span><span class="sxs-lookup"><span data-stu-id="b8aef-180">The following Azure table storage example is similar to the database example.</span></span> <span data-ttu-id="b8aef-181">它不包括所有需要若要开始使用 Azure 表存储服务的信息。</span><span class="sxs-lookup"><span data-stu-id="b8aef-181">It does not include all of the information that you would need to get started with Azure Table Storage Service.</span></span> <span data-ttu-id="b8aef-182">有关信息，请参阅[如何通过.NET 使用表存储](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-tables/)。</span><span class="sxs-lookup"><span data-stu-id="b8aef-182">For information, see [How to use Table storage from .NET](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-tables/).</span></span>

<span data-ttu-id="b8aef-183">下面的示例演示用于存储连接信息的表实体。</span><span class="sxs-lookup"><span data-stu-id="b8aef-183">The following example shows a table entity for storing connection information.</span></span> <span data-ttu-id="b8aef-184">它按用户名称，将数据分区，并由连接 id 标识每个实体，以便用户可以在任何时间有多个连接。</span><span class="sxs-lookup"><span data-stu-id="b8aef-184">It partitions the data by user name, and identifies each entity by the connection id, so a user can have multiple connections at any time.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample9.cs)]

<span data-ttu-id="b8aef-185">在中心，你可以跟踪每个用户的连接的状态。</span><span class="sxs-lookup"><span data-stu-id="b8aef-185">In the hub, you track the status of each user's connection.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample10.cs)]