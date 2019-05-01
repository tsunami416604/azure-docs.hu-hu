---
title: HeadPose használatával módosíthatja a négyszög meghatározása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható a HeadPose attribútum automatikusan rotálhatja a négyszög meghatározása.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: ddc5bc522c0d3ac258581f2a48a5c3b755302f01
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576502"
---
# <a name="use-the-headpose-attribute-to-adjust-the-face-rectangle"></a>A HeadPose attribútum segítségével módosíthatja a négyszög meghatározása

Ebben az útmutatóban egy felismert arc attribútum HeadPose, a Face objektum téglalap rotálása használandó. A minta-kódja ebben az útmutatóban a [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) mintaalkalmazást, .NET SDK-t használja.

A négyszög meghatározása, minden észlelt Face visszaadott jelöli meg a helyét és méretét, az arcok a képen. Alapértelmezés szerint téglalap mindig igazodik a lemezképpel (a oldalai tökéletesen vízszintes és függőleges); Ez lehet a Framing keretezési módot döntött arcok nem elég hatékony. Az olyan esetekben, ahol szeretné a programozott módon az arcok a képen körülvágása célszerű lehet levágni a téglalap rotálása.

## <a name="explore-the-sample-code"></a>Áttekintjük a mintakódot

Elforgathat programozott módon a négyszög meghatározása a HeadPose attribútum használatával. Ha ez az attribútum adja meg, amikor arcok észlelése (lásd: [arcok észlelése](HowtoDetectFacesinImage.md)), hajthat végre lekérdezést később lesz. A következő metódust a a [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) alkalmazás listáját használja **DetectedFace** objektumokat, és a egy listáját adja vissza **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** objektumok. **Face** itt az, hogy a tárolók között adatokat, beleértve a frissített téglalap koordináták egyéni osztály. Az új értékek kiszámítása **felső**, **bal oldali**, **szélesség**, és **magasság**, és a egy új mezőt **FaceAngle**elforgatási megadja.

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

## <a name="display-the-updated-rectangle"></a>A frissített téglalap megjelenítése

Itt is használhatja a visszaadott **Face** a megjelenített objektumok. A következő sorokat a [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) hogyan jelenik meg az új téglalap az adatok megjelenítése:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="next-steps"></a>További lépések

Tekintse meg a [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) alkalmazás a Githubon, az elforgatott arcjelző négyszögek működő példát. Vagy tekintse meg a [Face API HeadPose minta](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) alkalmazásra, amely nyomon követi a HeadPose attribútum valós időben észlelheti a különböző központi áthelyezések (bólogató alkotják, miközben).