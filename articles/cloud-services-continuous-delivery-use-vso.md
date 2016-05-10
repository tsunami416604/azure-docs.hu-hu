<properties urlDisplayName="Publishing with Visual Studio Online" pageTitle="Continuous delivery with Visual Studio Online in Azure" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure websites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen"></tags>

# Continuous delivery to Azure using Visual Studio Online

You can configure your Visual Studio Online team projects to automatically build and deploy to Azure websites or cloud services. (For information on how to set up a continuous build and deploy system using an *on-premises* Team Foundation Server, see [Continuous Delivery for Cloud Services in Azure][Continuous Delivery for Cloud Services in Azure].)

This tutorial assumes you have Visual Studio 2013 and the Azure SDK installed. If you don't already have Visual Studio 2013, download it by choosing the **Get started for free** link at [www.visualstudio.com][www.visualstudio.com]. Install the Azure SDK from [here][here].

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>You need an Visual Studio online account to complete this tutorial:</h5>
<p>You can <a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">open a Visual Studio Online account for free</a>.</p>
</div>

To set up a cloud service to automatically build and deploy to Azure by using Visual Studio Online, follow these steps:

-   [Step 1: Create a team project.][Step 1: Create a team project.]

-   [Step 2: Check in a project to source control.][Step 2: Check in a project to source control.]

-   [Step 3: Connect the project to Azure.][Step 3: Connect the project to Azure.]

-   [Step 4: Make changes and trigger a rebuild and redeployment.][Step 4: Make changes and trigger a rebuild and redeployment.]

-   [Step 5: Redeploy an earlier build (optional)][Step 5: Redeploy an earlier build (optional)]

-   [Step 6: Change the Production deployment (cloud services only)][Step 6: Change the Production deployment (cloud services only)]

-   [Step 7: Run unit tests (optional)][Step 7: Run unit tests (optional)]

## <a name="step1"></a><span class="short-header">Create a team project</span>Step 1: Create a team project

Follow the instructions [here][1] to create your team project and link it to Visual Studio. This walkthrough assumes you are using Team Foundation Version Control (TFVC) as your source control solution. If you want to use Git for version control, see [the Git version of this walkthrough][the Git version of this walkthrough].

## <a name="step2"> </a><span class="short-header">Check in a project to source control.</span>Step 2: Check in a project to source control

1.  In Visual Studio, open the solution you want to deploy, or create a new one.
    You can deploy a website or a cloud service (Azure Application) by following the steps in this walkthrough.
    If you want to create a new solution, create a new Azure Cloud Service project,
    or a new ASP.NET MVC project. Make sure that the project targets .NET Framework 4 or 4.5, and if you are creating a cloud service project, add an ASP.NET MVC web role and a worker role, and choose Internet application for the web role. When prompted, choose **Internet Application**.
    If you want to create a website, choose the ASP.NET Web Application project template, and then choose MVC. See [Get started with Azure and ASP.NET][Get started with Azure and ASP.NET].

2.  Open the context menu for the solution, and select **Add Solution to Source Control**.
    ![][0]

3.  Accept or change the defaults and choose the **OK** button. Once the process completes, source control icons appear in Solution Explorer.
    ![][2]

4.  Open the shortcut menu for the solution, and choose **Check In**.
    ![][3]

5.  In the Pending Changes area of Team Explorer, type a comment for the check-in and choose the **Check In** button.
    ![][4]

Note the options to include or exclude specific changes when you check in. If desired changes are excluded, choose the **Include All** link.
![][5]

## <a name="step3"> </a><span class="short-header">Connect the project to Azure</span>Step 3: Connect the project to Azure

1.  Now that you have a VSO team project with some source code in it, you are ready to connect your team project to Azure. In the [Azure Portal][Azure Portal], select your cloud service or website, or create a new one by selecting the + icon at the bottom left and choosing **Cloud Service** or **Website** and then **Quick Create**. Choose the **Set up publishing with Visual Studio Online** link.
    ![][6]

2.  In the wizard, type the name of your Visual Studio Online account in the textbox and click the **Authorize Now** link. You might be asked to sign in.
    ![][7]

3.  In the OAuth pop-up dialog, choose **Accept** to authorize Azure to configure your team project in VSO.
    ![][8]

4.  When authorization succeeds, you see a dropdown containing a list of your Visual Studio Online team projects. Select the name of team project that you created in the previous steps, and choose the wizard's checkmark button.
    ![][9]

5.  When your project is linked, you will see some instructions for checking in changes to your Visual Studio Online team project. On your next check-in, Visual Studio Online will build and deploy your project to Azure. Try this now by clicking the **Check In from Visual Studio** link, and then the **Launch Visual Studio** link (or the equivalent **Visual Studio** button at the bottom of the portal screen).
    ![][10]

## <a name="step4"> </a><span class="short-header">Trigger a rebuild</span>Step 4: Trigger a rebuild and redeploy your project

1.  In Visual Studio's Team Explorer, click the **Source Control Explorer** link.
    ![][11]

2.  Navigate to your solution file and open it.
    ![][12]

3.  In Solution Explorer, open up a file and change it. For example, change the file \_Layout.cshtml under the Views\\Shared folder in an MVC web role.
    ![][13]

4.  Edit the logo for the site and hit Ctrl+S to save the file.
    ![][14]

5.  In Team Explorer, choose the **Pending Changes** link.
    ![][15]

6.  Type in a comment and choose the **Check In** button.
    ![][16]

7.  Choose the Home button to return to the Team Explorer home page.
    ![][17]

8.  Choose the **Builds** link to view the builds in progress.
    ![][18]
    The Team Explorer shows that a build has been triggered for your check-in.
    ![][19]

9.  Double-click the name of the build in progress to view a detailed log as the build progresses.
    ![][20]

10. While the build is in-progress, take a look at the build definition that was created when you linked TFS to Azure by using the wizard. Open the shortcut menu for the build definition and choose **Edit Build Definition**.
    ![][21]
    In the **Trigger** tab, you will see that the build definition is set to build on every check-in by default.
    ![][22]
    In the **Process** tab, you can see the deployment environment is set to the name of your cloud service or website. If you are working with websites, the properties you see will be different from those shown here.
    ![][23]
    Specify values for the properties if you want different values than the defaults. The properties for Azure publishing are in the Deployment section.
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

If you are using multiple service configurations (.cscfg files), you can specify the desired service configuration in the **Build, Advanced, MSBuild arguments** setting. For example, to use ServiceConfiguration.Test.cscfg, set MSBuild arguments line option /p:TargetProfile=Test.
![][24]

1.  By this time, your build should be completed successfully.
    ![][25]

2.  If you double-click the build name, Visual Studio shows a **Build Summary**, including any test results from associated unit test projects.
    ![][26]

3.  In the [Azure Portal][Azure Portal], you can view the associated deployment on the Deployments tab when the staging environment is selected.
    ![][27]

4.  Browse to your site's URL. For a website, just click the Browse button on the command bar. For a cloud service, choose the URL in the **Quick Glance** section of the **Dashboard** page that shows the Staging environment for a cloud service. Deployments from continuous integration for cloud services are published to the Staging environment by default. You can change this by setting the Alternate Cloud Service Environment property to Production. This screenshot shows where the site URL is on the cloud service's dashboard page:
    ![][28]
    A new browser tab will open to reveal your running site.
    ![][29]

5.  For cloud services, if you make other changes to your project, you trigger more builds, and you will accumulate multiple deployments. The latest one marked as Active.
    ![][30]

## <a name="step5"> </a><span class="short-header">Redeploy an earlier build</span>Step 5: Redeploy an earlier build

This step applies to cloud services and is optional. In the management portal, select an earlier deployment and click the **Redeploy** button to rewind your site to an earlier check-in. Note that this will trigger a new build in TFS, and create a new entry in your deployment history.
![][31]

## <a name="step6"> </a><span class="short-header">Change the Production deployment</span>Step 6: Change the Production deployment

This step applies only to cloud services, not websites. When you are ready, you can promote the Staging environment to the production environment by choosing the Swap button in the management portal. The newly deployed Staging environment is promoted to Production, and the previous Production environment, if any, becomes a Staging environment. The Active deployment may be different for the Production and Staging environments, but the deployment history of recent builds is the same regardless of environment.
![][32]

## <a name="step7"> </a><span class="short-header">Run unit tests</span>Step 7: Run unit tests

To put a quality gate on your live or staging deployments, you can run unit tests and if they fail, you can stop the deployment.

1.  In Visual Studio, add a unit test project.
    ![][33]

2.  Add project references to the projects you want to test.
    ![][34]

3.  Add some unit tests. To get started, try a dummy test that will always pass.

        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }

4.  Edit the build definition, choose the Process tab, and expand the Test node.

5.  Set the **Fail build on test failure** to True. This means that the deployment won't occur unless the tests pass.
    ![][35]

6.  Queue a new build.
    ![][36]
    ![][37]

7.  While the build is proceeding, check on its progress.
    ![][38]
    ![][39]

8.  When the build is done, check the test results.
    ![][40]
    ![][41]

9.  Try creating a test that will fail. Add a new test by copying the first one, rename it, and comment out the line of code that throws the NotImplementedException.

        [TestMethod]
        [ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            //throw new NotImplementedException();
        }

10. Check in the change to queue a new build.
    ![][42]

11. View the test results to see details about the failure.
    ![][43]
    ![][44]

For more about unit testing in Visual Studio Online, see [Run unit tests in your build][Run unit tests in your build].

For more information, see [Visual Studio Online][Visual Studio Online]. If you're using Git, see [Share your code in Git][Share your code in Git] and [Publishing from Source Control to Azure Websites][Publishing from Source Control to Azure Websites].

  [Continuous Delivery for Cloud Services in Azure]: ../cloud-services-dotnet-continuous-delivery
  [www.visualstudio.com]: http://www.visualstudio.com
  [here]: http://go.microsoft.com/fwlink/?LinkId=239540
  [open a Visual Studio Online account for free]: http://go.microsoft.com/fwlink/p/?LinkId=512979
  [Step 1: Create a team project.]: #step1
  [Step 2: Check in a project to source control.]: #step2
  [Step 3: Connect the project to Azure.]: #step3
  [Step 4: Make changes and trigger a rebuild and redeployment.]: #step4
  [Step 5: Redeploy an earlier build (optional)]: #step5
  [Step 6: Change the Production deployment (cloud services only)]: #step6
  [Step 7: Run unit tests (optional)]: #step7
  [1]: http://go.microsoft.com/fwlink/?LinkId=512980
  [the Git version of this walkthrough]: http://go.microsoft.com/fwlink/p/?LinkId=397358
  [Get started with Azure and ASP.NET]: http://www.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-get-started/
  [0]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
  [2]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
  [3]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
  [4]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
  [5]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
  [Azure Portal]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
  [7]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
  [8]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
  [9]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
  [10]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
  [11]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
  [12]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
  [13]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [14]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
  [15]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
  [16]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
  [19]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [28]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [29]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [30]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [31]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [32]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [33]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
  [34]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
  [35]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
  [36]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
  [37]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
  [38]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
  [39]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
  [40]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
  [41]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
  [42]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
  [43]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
  [44]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
  [Run unit tests in your build]: http://go.microsoft.com/fwlink/p/?LinkId=510474
  [Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Share your code in Git]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [Publishing from Source Control to Azure Websites]: http://www.windowsazure.com/en-us/documentation/articles/web-sites-publish-source-control
