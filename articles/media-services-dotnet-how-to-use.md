<properties" pageTitle="How to Use Media Services" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use Media Services" authors="" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="juliako"></tags>

# How to Use Media Services

This guide shows you how to start programming with Azure Media Services. The guide consists of a set of articles and includes a technical overview of Media Services, steps to configure your Azure account for Media Services, a setup guide for development, and topics that show how to accomplish typical programming tasks. The scenarios demonstrated include: uploading assets, encrypting assets, encoding assets, and delivering assets.The samples are written in C# and use the Media Services SDK for .NET. For more information on Azure Media Services, refer to the [Next Steps][Next Steps] topic.

You can also program Media Services using the OData-based REST APIs. You can build an application making REST API calls to Media Services from .NET languages or other programming languages. For a full documentation series on programming with the Media Services REST API, see [Building Applications with the Azure Media Services REST API][Building Applications with the Azure Media Services REST API].

To start programming with the Media Services REST API or the Media Services SDK, first enable your Azure account for Media Services as described in the section [Setting Up an Azure Account for Media Services][Setting Up an Azure Account for Media Services].

The most up-to-date Media Services SDK documentation is located [here][here].

## <a name="what-are"></a><span class="short header">What are Media Services?</span>

Azure Media Services form an extensible media platform that integrates the best of the Microsoft Media Platform and third-party media components in Azure. Media Services provide a media pipeline in the cloud that enables industry partners to extend or replace component technologies. ISVs and media providers can use Media Services to build end-to-end media solutions. This overview describes the general architecture and common development scenarios for Media Services.

The following diagram illustrates the basic Media Services architecture.

![Media Services Architecture][Media Services Architecture]

### Media Services Feature Support

The current release of Media Services provides the following feature set for developing media applications in the cloud.

-   **Ingest**. Ingest operations bring assets into the system, for example by uploading them and encrypting them before they are placed into Azure Storage. Media Services offers integration with partner components to provide fast UDP (User Datagram Protocol) upload solutions.
-   **Encode**. Encode operations include encoding, transforming and converting media assets. You can run encoding tasks in the cloud using the Azure Media Encoder. Encoding options include the following:
-   Use the Azure Media Encoder and work with a range of standard codecs and formats, including industry-leading IIS Smooth Streaming, MP4, and conversion to Apple HTTP Live Streaming.
-   Convert entire libraries or individual files with total control over input and output.
-   A large set of supported file types, formats, and codecs (see [Supported File Types for Media Services][Supported File Types for Media Services]).
-   Supported format conversions. Media Services enable you to convert ISO MP4 (.mp4) to Smooth Streaming File Format (PIFF 1.3) (.ismv; .isma). You can also convert Smooth Streaming File Format (PIFF) to Apple HTTP Live Streaming (.msu8, .ts).
-   **Protect**. Protecting content means encrypting live streaming or on demand content for secure transport, storage, and delivery. Media Services provide a DRM technology-agnostic solution for protecting content. Currently supported DRM technologies are Microsoft PlayReady and MPEG Common Encryption. Support for additional DRM technologies will be available.
-   **Stream**. Streaming content involves sending it live or on demand to clients, or you can download specific media files from the cloud. Media Services provide a format-agnostic solution for streaming content. Media Services provide streaming origin support for Smooth Streaming, Apple HTTP Live Streaming, and MP4 formats. Support for additional formats will be available. You can also seamlessly deliver streaming content by using a third-party CDN, which enables the option to scale to millions of users.

### Media Services Development Scenarios

Media Services support several common media development scenarios as described in the following table.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<thead>

<tr>

<th>
Scenario

</th>

<th>
Description

</th>

</tr>

</thead>

<tbody>

<tr>

<td>
Building end-to-end workflows

</td>

<td>
Build comprehensive media workflows entirely in the cloud. From uploading media to distributing content, Media Services provide a range of components that can be combined to handle specific application workflows. Current capabilities include upload, storage, encoding, format conversion, content protection, and on-demand streaming delivery.

</td>

</tr>

<tr>

<td>
Building hybrid workflows

</td>

<td>
You can integrate Media Services with existing tools and processes. For example, encode content on-site then upload to Media Services for transcoding into multiple formats and deliver through Azure CDN, or a third-party CDN. Media Services can be called individually via standard REST APIs for integration with external applications and services.

</td>

</tr>

<tr>

<td>
Providing cloud support for media players

</td>

<td>
You can create, manage, and deliver media across multiple devices (including iOS, Android, and Windows devices) and platforms.

</td>

</tr>

</tbody>

</table>
</p>
### <a name="media-client"></a>Media Services Client Development

Extend the reach of your Media Services solution by using SDKs and player frameworks to build media client applications. These clients are for developers who want to build Media Services applications that offer compelling user experiences across a range of devices and platforms. Depending on the target devices, there are options for SDKs and player frameworks available from Microsoft and third-party partners.

The following provides a list of available client SDKs and player frameworks. For more information on these and other planned SDKs and player frameworks, and the functionality they can support, see [Media Services Client Development][Media Services Client Development] in the Media Services forums.

#### Mac and PC client support

For PCs and Macs you can target a streaming experience using Microsoft Silverlight or Adobe Open Source Media Framework.

-   [Smooth Streaming Client for Silverlight][Smooth Streaming Client for Silverlight]
-   [Microsoft Media Platform: Player Framework for Silverlight][Microsoft Media Platform: Player Framework for Silverlight]
-   [Smooth Streaming Plugin for OSMF 2.0][Smooth Streaming Plugin for OSMF 2.0]. For information on how to use this plug-in, see [How to Use Smooth Streaming Plugin for Adobe Open Source Media Framework][How to Use Smooth Streaming Plugin for Adobe Open Source Media Framework].

#### Windows 8 applications

For Windows 8, you can build Windows Store applications using any of the supported development languages and constructs like HTML, Javascript, XAML, C# and C+.

-   [Smooth Streaming Client SDK for Windows 8][Smooth Streaming Client SDK for Windows 8]. For more information on how to create a Windows Store application using this SDK, see [How to Build a Smooth Streaming Windows Store Application][How to Build a Smooth Streaming Windows Store Application]. For information on how to create a smooth streaming player in HTML5, see [Walkthrough: Building Your First HTML5 Smooth Streaming Player][Walkthrough: Building Your First HTML5 Smooth Streaming Player].

-   [Microsoft Media Platform: Player Framework for Windows 8 Windows Store Applications][Microsoft Media Platform: Player Framework for Windows 8 Windows Store Applications]

#### Xbox

Xbox supports Xbox LIVE applications that can consume Smooth Streaming content. The Xbox LIVE Application Development Kit (ADK) includes:

-   Smooth Streaming client for Xbox LIVE ADK
-   Microsoft Media Platform: Player Framework for Xbox LIVE ADK

#### Embedded or dedicated devices

Devices such as connected TVs, set-top boxes, Blu-Ray players, OTT TV boxes, and mobile devices that have a custom application development framework and a custom media pipeline. Microsoft provides the following porting kits that can be licensed, and enables partners to port Smooth Streaming playback for the platform.

-   [Smooth Streaming Client Porting Kit][Smooth Streaming Client Porting Kit]
-   [Microsoft PlayReady Device Porting Kit][Microsoft PlayReady Device Porting Kit]

#### Windows Phone

Microsoft provides an SDK that can be used to build premium video applications for Windows Phone.

-   [Smooth Streaming Client for Silverlight][Smooth Streaming Client for Silverlight]
-   [Microsoft Media Platform: Player Framework for Silverlight][Microsoft Media Platform: Player Framework for Silverlight]

#### iOS devices

For iOS devices including iPhone, iPod, and iPad, Microsoft ships an SDK that you can use to build applications for these platforms to deliver premium video content: Smooth Streaming SDK for iOS Devices with PlayReady. The SDK is available only to licensees, so for more information, please [email Microsoft][email Microsoft]. For information on iOS development, see the [iOS Developer Center][iOS Developer Center].

#### Android devices

Several Microsoft partners ship SDKs for the Android platform that add the capability to play back Smooth Streaming on an Android device. Please [email Microsoft][1] for more details on the partners.

## Next Steps

Now that you have an overview of Media Services, go to the [Setting Up Your Computer for Media Services][Setting Up Your Computer for Media Services] topic.

  [Next Steps]: #next-steps
  [Building Applications with the Azure Media Services REST API]: http://go.microsoft.com/fwlink/?linkid=252967
  [Setting Up an Azure Account for Media Services]: #setup-account
  [here]: http://msdn.microsoft.com/en-us/library/hh973613.aspx
  [Media Services Architecture]: ./media/media-services-dotnet-how-to-use/wams-01.png
  [Supported File Types for Media Services]: http://msdn.microsoft.com/en-us/library/dn535852.aspx
  [Media Services Client Development]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/thread/e9092ec6-2dfc-44cb-adce-1dc935309d2a
  [Smooth Streaming Client for Silverlight]: http://www.microsoft.com/en-us/download/details.aspx?id=29940
  [Microsoft Media Platform: Player Framework for Silverlight]: http://smf.codeplex.com/documentation
  [Smooth Streaming Plugin for OSMF 2.0]: http://go.microsoft.com/fwlink/?LinkId=275022
  [How to Use Smooth Streaming Plugin for Adobe Open Source Media Framework]: ../media-services-use-osmf-smooth-streaming-client-plugin/
  [Smooth Streaming Client SDK for Windows 8]: http://go.microsoft.com/fwlink/?LinkID=246146
  [How to Build a Smooth Streaming Windows Store Application]: http://go.microsoft.com/fwlink/?LinkId=271647
  [Walkthrough: Building Your First HTML5 Smooth Streaming Player]: http://msdn.microsoft.com/en-us/library/jj573656(v=vs.90).aspx
  [Microsoft Media Platform: Player Framework for Windows 8 Windows Store Applications]: http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home
  [Smooth Streaming Client Porting Kit]: http://www.microsoft.com/en-us/mediaplatform/sspk.aspx
  [Microsoft PlayReady Device Porting Kit]: http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx
  [email Microsoft]: mailto:askdrm@microsoft.com
  [iOS Developer Center]: https://developer.apple.com/devcenter/ios/index.action
  [1]: mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices
  [Setting Up Your Computer for Media Services]: ../media-services-set-up-computer/
