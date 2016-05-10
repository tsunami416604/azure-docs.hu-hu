<properties urlDisplayName="Publishing with Visual Studio Online" pageTitle="Continuous delivery with Visual Studio Online in Azure" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure websites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online and Git" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen"></tags>

# Continuous delivery to Azure using Visual Studio Online and Git

You can use Visual Studio Online team projects to host a Git repository for your source code, and automatically build and deploy to Azure websites or cloud services whenever you push a commit to the repository.

You'll need Visual Studio 2013 and the Azure SDK installed. If you don't already have Visual Studio 2013, download it by choosing the **Get started for free** link at [www.visualstudio.com][www.visualstudio.com]. Install the Azure SDK from [here][here].

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>You need an Visual Studio online account to complete this tutorial:</h5>
<p>You can <a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">open a Visual Studio Online account for free</a>.</p>
</div>

To set up a cloud service to automatically build and deploy to Azure by using Visual Studio Online, follow these steps:

-   [Step 1: Create a Git repository.][Step 1: Create a Git repository.]

-   [Step 2: Create a project and push it to your Git repository.][Step 2: Create a project and push it to your Git repository.]

-   [Step 3: Connect the project to Azure.][Step 3: Connect the project to Azure.]

-   [Step 4: Make changes and trigger a rebuild and redeployment.][Step 4: Make changes and trigger a rebuild and redeployment.]

-   [Step 5: Redeploy an earlier build (optional)][Step 5: Redeploy an earlier build (optional)]

-   [Step 6: Change the Production deployment][Step 6: Change the Production deployment]

-   [Step 7: Deploy from a working branch][Step 7: Deploy from a working branch]

## <a name="step1"></a><span class="short-header">Step 1: Create a Git repository.</span>Step 1: Create a Git repository

1.  If you don’t yet have a Visual Studio Online account, follow the instructions [here][1]. When you create your team project, choose Git as your source control system. Follow the instructions to connect Visual Studio to your team project.

2.  In Team Explorer, choose the **Clone this repository** link.
    ![][0]

3.  Specify the location of the local copy, and choose the **Clone** button.

## <a name="step2"> </a><span class="short-header">Create a project and commit it to the repository.</span>Step 2: Create a project and commit it to the repository

1.  In Team Explorer, in the Solutions section, choose the New link to create a new project in the local repository.
    ![][2]

2.  You can deploy a website or a cloud service (Azure Application) by following the steps in this walkthrough.
    Create a new Windows Azure Cloud Service project,
    or a new ASP.NET MVC project. Make sure that the project targets the .NET Framework 4 or 4.5, and if you are creating a cloud service project, add an ASP.NET MVC web role and a worker role.
    If you want to create a website, choose the ASP.NET Web Application project template, and then choose MVC. See [Get started with Azure and ASP.NET][Get started with Azure and ASP.NET].

3.  Open the shortcut menu for the solution, and choose **Commit**.
    ![][3]

4.  If this is the first time you've used Git in Visual Studio Online, you'll need to provide some information to identify yourself in Git. In the **Pending Changes** area of Team Explorer, enter your username and email address. Type a comment for the commit and choose **Commit**.
    ![][4]

5.  Note the options to include or exclude specific changes when you check in. If the changes you want are excluded, choose **Include All**.

6.  You've now committed the changes in your local copy of the repository. Next, sync those changes with the server. Choose the **Sync** link.

## <a name="step3"> </a><span class="short-header">Connect the project to Azure</span>Step 3: Connect the project to Azure

1.  Now that you have a Git repository in Visual Studio Online with some source code in it, you are ready to connect your git repository to Azure. In the [Azure Portal][Azure Portal], select your cloud service or website, or create a new one by selecting the + icon at the bottom left and choosing **Cloud Service** or **Website** and then **Quick Create**.<br.>
    ![][5]

2.  For cloud services, choose the **Set up publishing with Visual Studio Online** link. For websites, choose the **Set up deployment from source control** link.
    ![][6]

3.  In the wizard, type the name of your Visual Studio Online account in the textbox and choose the **Authorize Now** link. You might be asked to sign in.
    ![][7]

4.  In the OAuth pop-up dialog, choose **Accept** to authorize Azure to configure your team project in Visual Studio Online.
    ![][8]

5.  When authorization succeeds, you see a dropdown list that contains your Visual Studio Online team projects. Select the name of team project that you created in the previous steps, and choose the wizard's checkmark button.
    ![][9]

The next time you push a commit to your repository, Visual Studio Online will build and deploy your project to Azure.

## <a name="step4"> </a><span class="short-header">Trigger a rebuild</span>Step 4: Trigger a rebuild and redeploy your project

1.  In Visual Studio, open up a file and change it. For example, change the file \_Layout.cshtml under the Views\\Shared folder in an MVC web role.
    ![][10]

2.  Edit the footer text for the site and save the file.
    ![][11]

3.  In Solution Explorer, open the shortcut menu for the solution node, project node, or the file you changed, and choose **Commit**.

4.  Type in a comment and choose **Commit**.
    ![][4]

5.  Choose the **Sync** link.
    ![][12]

6.  Choose the **Push** link to push your commit to the repository in Visual Studio Online. (You can also use the **Sync** button to copy your commits to the repository. The difference is that **Sync** also pulls the latest changes from the repository.)
    ![][13]

7.  Choose the Home button to return to the Team Explorer home page.
    ![][14]

8.  Choose **Builds** to view the builds in progress.
    ![][15]
    Team Explorer shows that a build has been triggered for your check-in.
    ![][16]

9.  To view a detailed log as the build progresses, double-click the name of the build in progress.

10. While the build is in-progress, take a look at the build definition that was created when you used the wizard to link to Azure. Open the shortcut menu for the build definition and choose **Edit Build Definition**.
    ![][17]
    In the **Trigger** tab, you will see that the build definition is set to build on every check-in, by default. (For a cloud service, Visual Studio Online builds and deploys the master branch to the staging environment automatically. You still have to do a manual step to deploy to the live site. For a website that doesn't have staging environment, it deploys the master branch directly to the live site.
    ![][18]
    In the **Process** tab, you can see the deployment environment is set to the name of your cloud service or website.
    ![][19]
    Specify values for the properties if you want different values than the defaults. The properties for Azure publishing are in the Deployment section, and you might also need to set MSBuild parameters. For example, in a cloud service project, to specify a service configuration other than "Cloud", set the MSbuild parameters to /p:TargetProfile=*YourProfile* where *YourProfile* matches a service configuration file with a name like ServiceConfiguration.*YourProfile*.cscfg.
    The following table shows the available properties in the Deployment section:

    <table>

    <tr>
    <td>
    **Property**

    </td>
    <td>
    **Default Value**

    </td>
    </tr>
    </p>
    > <tr>
    > <td>
    > Allow Untrusted Certificates
    >
    > </td>
    > <td>
    > If false, SSL certificates must be signed by a root authority.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Allow Upgrade
    >
    > </td>
    > <td>
    > Allows a the deployment to update an existing deployment instead of creating a new one. Preserves the IP address.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Do Not Delete
    >
    > </td>
    > <td>
    > If true, do not overwrite an existing unrelated deployment (upgrade is allowed).
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Path to Deployment Settings
    >
    > </td>
    > <td>
    > The path to your .pubxml file for a website, relative to the root folder of the repo. Ignored for cloud services.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Sharepoint Deployment Environment
    >
    > </td>
    > <td>
    > The same as the service name
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Windows Azure Deployment Environment
    >
    > </td>
    > <td>
    > The website or cloud service name
    >
    > </td>
    > </tr>
    > </table>

11. By this time, your build should be completed successfully.
    ![][20]

12. If you double-click the build name, Visual Studio shows a **Build Summary**, including any test results from associated unit test projects.
    ![][21]

13. In the [Azure Portal][Azure Portal], you can view the associated deployment on the Deployments tab when the staging environment is selected.
    ![][22]

14. Browse to your site's URL. For a website, just choose the **Browse** button in the portal. For a cloud service, choose the URL in the **Quick Glance** section of the **Dashboard** page that shows the Staging environment. Deployments from continuous integration for cloud services are published to the Staging environment by default. You can change this by setting the Alternate Cloud Service Environment property to Production. Here's where the site URL is on the cloud service's dashboard page:
    ![][23]
    A new browser tab will open to reveal your running site.
    ![][24]

15. If you make other changes to your project, you trigger more builds, and you will accumulate multiple deployments. The latest one is marked as Active.
    ![][25]

## <a name="step5"> </a><span class="short-header">Redeploy an earlier build</span>Step 5: Redeploy an earlier build

This step is optional. In the management portal, select an earlier deployment and click **Redeploy** to rewind your site to an earlier check-in. Note that this will trigger a new build in TFS, and create a new entry in your deployment history.
![][26]

## <a name="step6"> </a><span class="short-header">Change the Production deployment</span>Step 6: Change the Production deployment

When you are ready, you can promote the Staging environment to the production environment by choosing **Swap** in the management portal. The newly deployed Staging environment is promoted to Production, and the previous Production environment, if any, becomes a Staging environment. The Active deployment may be different for the Production and Staging environments, but the deployment history of recent builds is the same regardless of environment.
![][27]

## <a name="step7"> </a><span class="short-header">Deploy from a working branch</span>Step 6: Deploy from a working branch.

When you use Git, you usually make changes in a working branch and integrate into the master branch when your development reaches a finished state. During the development phase of a project, you'll want to build and deploy the working branch to Azure.

1.  In Team Explorer, choose the **Home** button and then choose the **Branches** button.
    ![][28]

2.  Choose the **New Branch** link.
    ![][29]

3.  Enter the name of the branch, such as "working," and choose **Create Branch**. This creates a new local branch.
    ![][30]

4.  Publish the branch. Choose the branch name in **Unpublished branches**, and choose **Publish**.
    ![][31]

5.  By default, only changes to the master branch trigger a continuous build. To set up continuous build for a working branch, choose the Builds page in Team Explorer, and choose **Edit Build Definition**.

6.  Open the **Source Settings** tab. Under **Monitored branches for continuous integration and build**, choose **Click here to add a new row**.
    ![][32]

7.  Specify the branch you created, such as refs/heads/working.
    ![][33]

8.  Make a change in the code, open the shortcut menu for the changed file, and choose **Commit**.
    ![][4]

9.  Choose the **Unsynced Commits** link, and choose the **Sync** button or the **Push** link to copy the changes to the copy of the working branch in Visual Studio Online.
    ![][12]

10. Navigate to the **Builds** view and find the build that just got triggered for the working branch.

For more information, see [Visual Studio Online][Visual Studio Online]. For additional tips on using Git with Visual Studio Online, see [Share your code in Git][Share your code in Git] and for information about using a Git repository that's not managed by Visual Studio Online to publish to Azure, see [Publishing from Source Control to Azure Websites][Publishing from Source Control to Azure Websites].

  [www.visualstudio.com]: http://www.visualstudio.com
  [here]: http://go.microsoft.com/fwlink/?LinkId=239540
  [open a Visual Studio Online account for free]: http://go.microsoft.com/fwlink/p/?LinkId=512979
  [Step 1: Create a Git repository.]: #step1
  [Step 2: Create a project and push it to your Git repository.]: #step2
  [Step 3: Connect the project to Azure.]: #step3
  [Step 4: Make changes and trigger a rebuild and redeployment.]: #step4
  [Step 5: Redeploy an earlier build (optional)]: #step5
  [Step 6: Change the Production deployment]: #step6
  [Step 7: Deploy from a working branch]: #step7
  [1]: http://go.microsoft.com/fwlink/?LinkId=397665
  [0]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
  [2]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
  [Get started with Azure and ASP.NET]: http://www.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-get-started/
  [3]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
  [4]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
  [Azure Portal]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
  [6]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
  [7]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
  [8]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
  [9]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
  [10]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [11]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
  [12]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
  [13]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
  [14]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
  [15]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
  [16]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [19]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [28]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
  [29]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
  [30]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
  [31]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
  [32]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
  [33]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
  [Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Share your code in Git]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [Publishing from Source Control to Azure Websites]: http://www.windowsazure.com/en-us/documentation/articles/web-sites-publish-source-control
