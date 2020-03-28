---
title: A HeadPose attribútum használata
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja a HeadPose attribútumot az arc téglalapjának automatikus elforgatására vagy a videohírfolyamban lévő fejkézmozdulatok észlelésére.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 534846044770d66ec5171ad4f61de921d2d5d194
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169787"
---
# <a name="use-the-headpose-attribute"></a>A HeadPose attribútum használata

Ebben az útmutatóban láthatja, hogyan használhatja az észlelt arc HeadPose attribútumát néhány kulcsfontosságú forgatókönyv engedélyezéséhez.

## <a name="rotate-the-face-rectangle"></a>Az arc téglalapjának elforgatása

Az arc téglalapja, amelyet minden észlelt arckal visszaadnak, jelzi az arc helyét és méretét a képen. Alapértelmezés szerint a téglalap mindig a képhez igazodik (oldalai függőlegesek és vízszintesek); ez nem hatékony a ferde lapok kialakításához. Olyan esetekben, amikor programozott módon szeretné körülvágni a képek et, jobb, ha a téglalapot elforgatja a körülvágáshoz.

A [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) mintaalkalmazás a HeadPose attribútumot használja az észlelt arctéglalapok elforgatásához.

### <a name="explore-the-sample-code"></a>A mintakód felfedezése

A HeadPose attribútum segítségével programozottmódon elforgathatja az arctéglalapot. Ha ezt az attribútumot az arcok észlelésekor adja meg (lásd: [Arcok észlelése),](HowtoDetectFacesinImage.md)később lekérdezheti azt. A cognitive [services face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) alkalmazás következő módszere az **DetectedFace objektumok** listáját tartalmazza, és visszaadja az **[Arcobjektumok](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** listáját. **A Face** itt egy egyéni osztály, amely arcadatokat tárol, beleértve a frissített téglalap koordinátákat is. A program a **felső,** **bal**, **szélesség**és **magasság**értéket, az új **FaceAngle** mező pedig az elforgatást határozza meg.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>A frissített téglalap megjelenítése

Itt használhatja a visszaadott **Face** objektumokat a kijelzőn. A [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) következő sorai bemutatják, hogyan jelenik meg az új téglalap ezekből az adatokból:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Fejmozdulatok észlelése

A HeadPose változások valós idejű követésével észlelheti az olyan fejmozdulatokat, mint a bólintás és a fejremegés. Ezt a funkciót egyéni élősség-érzékelőként is használhatja.

A liveness észlelése annak meghatározása, hogy a téma valós személy-e, és nem kép- vagy videoábrázolás. A fejgesztus-detektor az egyik módja annak, hogy segítsen ellenőrizni az élést, különösen, ellentétben egy személy képábrázolásával.

> [!CAUTION]
> A fejkézmozdulatok valós idejű észleléséhez a Face API-t magas (másodpercenként több mint egyszer) kell meghívnia. Ha ingyenes (f0) előfizetéssel rendelkezik, ez nem lehetséges. Ha fizetős szintű előfizetéssel rendelkezik, győződjön meg arról, hogy kiszámította a gyors API-hívások költségeit a fejgesztus-észleléshez.

Tekintse meg a [Face HeadPose minta](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) a GitHubon egy működő példa a fejgesztus-észlelés.

## <a name="next-steps"></a>További lépések

Tekintse meg a [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) alkalmazást a GitHubon egy működő példa az elforgatott arc téglalapok. Vagy tekintse meg a [Face HeadPose Sample](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) alkalmazást, amely valós időben követi nyomon a HeadPose attribútumot a fejmozgások észleléséhez.