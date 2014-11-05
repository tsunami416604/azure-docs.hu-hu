<properties urlDisplayName="Manage Assets in Media Services" pageTitle="How to Manage Assets in Media Services - Azure" metaKeywords="" description="Learn how to manage assets on Media Services. You can also manage jobs, tasks, access policies, locators, and more. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Manage Assets in storage" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako"></tags>

# How to: Manage Assets in storage

This article is one in a series introducing Azure Media Services programming. The previous topic was [How to: Protect an Asset][How to: Protect an Asset].

After you create media assets and upload them to Media Services, you can access and manage the assets on the server. You can also manage other objects on the server that are part of Media Services, including jobs, tasks, access policies, locators, and more.

The following example shows how to query for an asset by assetId.

    static IAsset GetAsset(string assetId){ // Use a LINQ Select query to get an asset. var assetInstance = from a in _context.Assets where a.Id == assetId select a; // Reference the asset as an IAsset. IAsset asset = assetInstance.FirstOrDefault();
    return asset;

<p>
}
</code>

</pre>
</p>
To list all assets available on the server, you can use the following method which iterates through the assets collection, and display details about each asset.

    static void ListAssets(){ string waitMessage = "Building the list. This may take a few " + "seconds to a few minutes depending on how many assets " + "you have." + Environment.NewLine + Environment.NewLine + "Please wait..." + Environment.NewLine; Console.Write(waitMessage);
    // Create a Stringbuilder to store the list that we build. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Display the collection of assets.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Display the files associated with each asset. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Display output in console.
    Console.Write(builder.ToString());

<p>
}
</code>

</pre>
The following code snippet deletes all the assets from the Media Services account.

    foreach (IAsset asset in _context.Assets){ asset.Delete();}

</p>
For more information about managing assets, see:

-   [Manage Assets with the Media Services SDK for .NET][Manage Assets with the Media Services SDK for .NET]
-   [Manage Assets with the Media Services REST API][Manage Assets with the Media Services REST API]

</p>
## Next Steps

Now that you know how to manage assets, go to the [How to Deliver an Asset by Download][How to Deliver an Asset by Download] topic.

  [How to: Protect an Asset]: ../media-services-protect-asset/
  [Manage Assets with the Media Services SDK for .NET]: http://msdn.microsoft.com/en-us/library/jj129589.aspx
  [Manage Assets with the Media Services REST API]: http://msdn.microsoft.com/en-us/library/jj129583.aspx
  [How to Deliver an Asset by Download]: ../media-services-deliver-asset-download/
