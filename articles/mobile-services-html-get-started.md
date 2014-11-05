<properties pageTitle="Get Started with Azure Mobile Services for HTML 5 apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for HTML development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Get started with Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>This tutorial shows you how to add a cloud-based backend service to an HTML app using Azure Mobile Services. In this tutorial, you will create both a new mobile service and a simple <em>To do list</em> app that stores app data in the new mobile service. You can view a video version of this tutorial by clicking the clip to the right.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">watch the tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">Play Video</span></a> <span class="time">3:51</span></div>

</div>

A screenshot from the completed app is below:

![][0]

Completing this tutorial is a prerequisite for all other Mobile Services tutorials for HTML apps.

<div class="dev-callout"><strong>Note</strong> <p>To complete this tutorial, you need an Azure account. If you don't have an account, you can create a free trial account in just a couple of minutes. For details, see <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F" target="_blank">Azure Free Trial</a>.</p></div>

### Additional requirements

-   This tutorial requires that you have one of the following web servers running on your local computer:

    -   **On Windows**: IIS Express. IIS Express is installed by the [Microsoft Web Platform Installer][Microsoft Web Platform Installer].
    -   **On MacOS X**: Python, which should already be installed.
    -   **On Linux**: Python. You must install the [latest version of Python][latest version of Python].

    You can use any web server to host the app, but these are the web servers that are supported by the downloaded scripts.

-   A web browser that supports HTML5.

## <a name="create-new-service"> </a>Create a new mobile service

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 

## <span class="short-header">Create a new app</span>Create a new HTML app

</h2>
Once you have created your mobile service, you can follow an easy quickstart in the Management Portal to either create a new app or modify an existing app to connect to your mobile service.

In this section you will create a new HTML app that is connected to your mobile service.

1.  In the Management Portal, click **Mobile Services**, and then click the mobile service that you just created.

2.  In the quickstart tab, click **Windows** under **Choose platform** and expand **Create a new HTML app**.

    ![][1]

    This displays the three easy steps to create and host an HTML app connected to your mobile service.

    ![][2]

3.  Click **Create TodoItems table** to create a table to store app data.

4.  Under **Download and run your app**, click **Download**.

    This downloads the website files for the sample *To do list* application that is connected to your mobile service. Save the compressed file to your local computer, and make a note of where you save it.

5.  In the **Configure** tab, verify that `localhost` is already listed in the **Allow requests from host names** list under **Cross-Origin Resource Sharing (CORS)**. If it's not, type `localhost` in the **Host name** field and then click **Save**.

    ![][3]

    <div class="dev-callout"><b>Note</b>
    <p>If you deploy the quickstart app to a web server other than localhost, you must add the host name of the web server to the <strong>Allow requests from host names</strong> list. For more information, see <a href="http://msdn.microsoft.com/en-us/library/windowsazure/dn155871.aspx" target="_blank">Cross-origin resource sharing</a>.</p>
</div>

## Host and run your HTML app

The final stage of this tutorial is to host and run your new app on your local computer.

1.  Browse to the location where you saved the compressed project files, expand the files on your computer, and launch one of the following command files from the **server** subfolder.

    -   **launch-windows** (Windows computers)
    -   **launch-mac.command** (Mac OS X computers)
    -   **launch-linux.sh** (Linux computers)

    <div class="dev-callout"><b>Note</b>
    <p>On a Windows computer, type `R` when PowerShell asks you to confirm that you want to run the script. Your web browser might warn you to not run the script because it was downloaded from the internet. When this happens, you must request that the browser proceed to load the script.</p>
</div>

    This starts a web server on your local computer to host the new app.

2.  Open the URL <http://localhost:8000/> in a web browser to start the app.

3.  In the app, type meaningful text, such as *Complete the tutorial*, in **Enter new task**, and then click **Add**.

    ![][4]

    This sends a POST request to the new mobile service hosted in Azure. Data from the request is inserted into the TodoItem table. Items stored in the table are returned by the mobile service, and the data is displayed in the second column in the app.

    <div class="dev-callout"> 
<b>Note</b> 
<p>You can review the code that accesses your mobile service to query and insert data, which is found in the app.js file.</p> 
</div>

4.  Back in the Management Portal, click the **Data** tab and then click the **TodoItems** table.

    ![][5]

    This lets you browse the data inserted by the app into the table.

    ![][6]

## <a name="next-steps"> </a>Next Steps

Now that you have completed the quickstart, learn how to perform additional important tasks in Mobile Services:

-   **[Get started with data][Get started with data]**
    Learn more about storing and querying data using Mobile Services.

-   **[Call a custom API from an HTML application][Call a custom API from an HTML application]**
    Connect your HTML application with a custom API hosted on Mobile Services.

-   **[Get started with authentication][Get started with authentication]**
    Learn how to authenticate users of your app with an identity provider.

-   **[Mobile Services HTML/JavaScript How-to Conceptual Reference][Mobile Services HTML/JavaScript How-to Conceptual Reference]**
    Learn more about how to use Mobile Services with HTML/JavaScript

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [watch the tutorial]: http://go.microsoft.com/fwlink/?LinkId=287040
  [0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png
  [Azure Free Trial]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F
  [Microsoft Web Platform Installer]: http://go.microsoft.com/fwlink/p/?LinkId=286333
  [latest version of Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
  [2]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png
  [3]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
  [Cross-origin resource sharing]: http://msdn.microsoft.com/en-us/library/windowsazure/dn155871.aspx
  [4]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
  [5]: ./media/mobile-services-html-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-html-get-started/mobile-data-browse.png
  [Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-html
  [Call a custom API from an HTML application]: /en-us/documentation/articles/mobile-services-html-call-custom-api
  [Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-html
  [Mobile Services HTML/JavaScript How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-html-js-client
