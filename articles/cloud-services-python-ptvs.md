<properties linkid="develop-python-cloud-services-with-ptvs" urlDisplayName="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" pageTitle="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" metaKeywords="Azure python, web role, worker role, PTVS, cloud service" description="Overview of using Python Tools for Visual Studio to create Azure cloud services including web roles and worker roles." metaCanonical="" services="" documentationCenter="Python" title="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo"></tags>

# Python Web and Worker Roles with Python Tools 2.1 for Visual Studio

This guide provides an overview of using Python web and worker roles using [Python Tools for Visual Studio][Python Tools for Visual Studio].

-   [Prerequisites][Prerequisites]
-   [What are Python web and worker roles?][What are Python web and worker roles?]
-   [Project creation][Project creation]
-   [Run locally][Run locally]
-   [Publish to Azure][Publish to Azure]
-   [Next steps][Next steps]

## <a name="prerequisites"></a>Prerequisites

-   Visual Studio 2012 or 2013
-   [Python Tools 2.1 for Visual Studio][Python Tools 2.1 for Visual Studio]
-   [Azure SDK Tools for VS 2013][Azure SDK Tools for VS 2013] or [Azure SDK Tools for VS 2012][Azure SDK Tools for VS 2012]
-   [Python 2.7 32-bit][Python 2.7 32-bit] or [Python 3.4 32-bit][Python 3.4 32-bit]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>What are Python web and worker roles?

Azure provides three compute models for running applications: [Azure Web Sites][Azure Web Sites], [Azure Virtual Machines][Azure Virtual Machines], and [Azure Cloud Services][Azure Cloud Services]. All three models support Python. Cloud Services, which include web and worker roles, provide *Platform as a Service (PaaS)*. Within a cloud service, a web role provides a dedicated Internet Information Services (IIS) web server to host front-end web applications, while a worker role can run asynchronous, long-running or perpetual tasks independent of user interaction or input.

For more information, see [What is a Cloud Service?][What is a Cloud Service?].

<div class="dev-callout"><strong>Looking to build a simple website?</strong>
<p>If your scenario involves just a simple website front-end, consider using a lightweight Azure Website. You can easily upgrade to a Cloud Service as your website grows and your requirements change. See the <a href="/en-us/develop/python/">Python Developer Center</a> for articles that cover development of Azure Websites.</p>
</div>

## <a name="project-creation"></a>Project creation

In Visual Studio, you can select **Azure Cloud Service** in the **New Project** dialog, under **Python**.

![New Project Dialog][New Project Dialog]

In the Azure Cloud Service wizard, you can select to create new web and worker roles.

![Azure Cloud Service Dialog][Azure Cloud Service Dialog]

The worker role template comes with boilerplate code to connect to a Azure storage account or service bus.

![Cloud Service Solution][Cloud Service Solution]

You can add web or worker roles to an existing cloud service at any time. You can choose to add existing projects in your solution, or create new ones.

![Add Role Command][Add Role Command]

Your cloud service can contain roles implemented in different languages. For example, you can have a Python web role implemented using Django, with Python and C# worker roles. You can easily communicate between your roles using service bus queues or storage queues.

## <a name="run-locally"></a>Run locally

If you set your cloud service project as the startup project and press F5, the cloud service will run in the local Azure emulator.

Although PTVS supports launching in the emulator, debugging (breakpoints, etc) will not work.

To debug your web and worker roles, you can set the role project as the startup project and debug that instead. You can also set multiple startup projects. Right-click on the solution and select **Set StartUp Projects**.

![Solution Startup Project Properties][Solution Startup Project Properties]

## <a name="publish-to-azure"></a>Publish to Azure

To publish, right-click on the cloud service project in the solution and select **Publish**.

![Microsoft Azure Publish Sign In][Microsoft Azure Publish Sign In]

In the settings page, select the cloud service you want to publish to.

![Microsoft Azure Publish Settings][Microsoft Azure Publish Settings]

You can create a new cloud service if you don't already have one available.

![Create Cloud Service Dialog][Create Cloud Service Dialog]

It's also useful to enable remote desktop connections to the machine(s) for debugging failures.

![Remote Desktop Configuration Dialog][Remote Desktop Configuration Dialog]

When you are done configuring settings, click **Publish**.

Some progress will appear in the output window, then you'll see the Microsoft Azure Activity Log window.

![Microsoft Azure Activity Log Window][Microsoft Azure Activity Log Window]

Deployment will take several minutes to complete, then your web and/or worker roles will be running on Azure!

## <a name="next-steps"></a>Next steps

For more detailed information on working with web and worker roles in Python Tools for Visual Studio, see the PTVS documentation:

-   [Cloud Service Projects][Cloud Service Projects]

For more details on using Azure services from your web and worker roles, such as using Azure storage or service bus, see the following guides:

-   [Blob Service][Blob Service]
-   [Table Service][Table Service]
-   [Queue Service][Queue Service]
-   [Service Bus Queues][Service Bus Queues]
-   [Service Bus Topics][Service Bus Topics]

<!--Link references--> <!--External Link references-->

  [Python Tools for Visual Studio]: http://pytools.codeplex.com
  [Prerequisites]: #prerequisites
  [What are Python web and worker roles?]: #what-are-python-web-and-worker-roles
  [Project creation]: #project-creation
  [Run locally]: #run-locally
  [Publish to Azure]: #publish-to-azure
  [Next steps]: #next-steps
  [Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
  [Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
  [Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
  [Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190
  [Python 3.4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Azure Web Sites]: /en-us/documentation/articles/fundamentals-application-models/#WebSites
  [Azure Virtual Machines]: /en-us/documentation/articles/fundamentals-application-models/#VMachine
  [Azure Cloud Services]: /en-us/documentation/articles/fundamentals-application-models/#CloudServices
  [What is a Cloud Service?]: /en-us/manage/services/cloud-services/what-is-a-cloud-service/
  [Python Developer Center]: /en-us/develop/python/
  [New Project Dialog]: ./media/cloud-services-python-ptvs/new-project-cloud-service.png
  [Azure Cloud Service Dialog]: ./media/cloud-services-python-ptvs/new-service-wizard.png
  [Cloud Service Solution]: ./media/cloud-services-python-ptvs/worker.png
  [Add Role Command]: ./media/cloud-services-python-ptvs/add-new-or-existing-role.png
  [Solution Startup Project Properties]: ./media/cloud-services-python-ptvs/startup.png
  [Microsoft Azure Publish Sign In]: ./media/cloud-services-python-ptvs/publish-sign-in.png
  [Microsoft Azure Publish Settings]: ./media/cloud-services-python-ptvs/publish-settings.png
  [Create Cloud Service Dialog]: ./media/cloud-services-python-ptvs/publish-create-cloud-service.png
  [Remote Desktop Configuration Dialog]: ./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png
  [Microsoft Azure Activity Log Window]: ./media/cloud-services-python-ptvs/publish-activity-log.png
  [Cloud Service Projects]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
  [Blob Service]: /en-us/documentation/articles/storage-python-how-to-use-blob-storage/
  [Table Service]: /en-us/documentation/articles/storage-python-how-to-use-table-storage/
  [Queue Service]: /en-us/documentation/articles/storage-python-how-to-use-queue-storage/
  [Service Bus Queues]: /en-us/documentation/articles/service-bus-python-how-to-use-queues/
  [Service Bus Topics]: /en-us/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/
