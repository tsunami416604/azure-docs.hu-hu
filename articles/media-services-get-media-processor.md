<properties urlDisplayName="Create a Media Processor" pageTitle="How to Create a Media Processor - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/26/2014" ms.author="juliako"></tags>

# How to: Get a Media Processor Instance

This article is one in a series introducing Azure Media Services programming. The previous topic was [How to: Create an Encrypted Asset and Upload into Storage][How to: Create an Encrypted Asset and Upload into Storage].

In Media Services a media processor is a component that handles a specific processing task, such as encoding, format conversion, encrypting, or decrypting media content. You typically create a media processor when you are creating a task to encode, encrypt, or convert the format of media content.

The following table provides the name and description of each available media processor.

| Media Processor Name          | Description                                                                                                                                                                  | More Information                                      |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|
| Azure Media Encoder           | Lets you run encoding tasks using the Media Encoder.                                                                                                                         | [Task Preset Strings for the Azure Media Encoder][Task Preset Strings for the Azure Media Encoder]   |
| Windows Azure Media Packager  | Lets you convert media assets from .mp4 to smooth streaming format. Also, lets you convert media assets from smooth streaming to the Apple HTTP Live Streaming (HLS) format. | [Task Preset Strings for the Azure Media Packager][Task Preset Strings for the Azure Media Packager]  |
| Windows Azure Media Encryptor | Lets you encrypt media assets using PlayReady Protection.                                                                                                                    | [Task Preset Strings for the Azure Media Packager][1] |
| Azure Media Indexer           | Enables you to make media files and content searchable, as well as generate closed captioning tracks and keywords.                                                           | N/A                                                   |
| Storage Decryption            | Lets you decrypt media assets that were encrypted using storage encryption.                                                                                                  | N/A                                                   |

The following method shows how to get a media processor instance. The code example assumes the use of a module-level variable named \*\*\_context\*\* to reference the server context as described in the section [How to: Connect to Media Services Programmatically][How to: Connect to Media Services Programmatically].

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
         var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

## Next Steps

Now that you know how to get a media processor instance, go to the [How to Encode an Asset][How to Encode an Asset] topic which will show you how to use the Azure Media Encoder to encode an asset.

  [How to: Create an Encrypted Asset and Upload into Storage]: ../media-services-create-encrypted-asset-upload-storage/
  [Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/en-us/library/jj129582.aspx
  [Task Preset Strings for the Azure Media Packager]: http://msdn.microsoft.com/en-us/library/hh973635.aspx
  [1]: http://msdn.microsoft.com/en-us/library/hh973610.aspx
  [How to: Connect to Media Services Programmatically]: ../media-services-set-up-computer/
  [How to Encode an Asset]: ../media-services-encode-asset/
