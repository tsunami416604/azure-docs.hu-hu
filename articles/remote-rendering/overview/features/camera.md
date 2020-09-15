---
title: Kamera
description: A kamera beállításait és használati eseteit ismerteti
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: 5ad39ad9f5e585029cff5d573a026702f259607e
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564848"
---
# <a name="camera"></a>Kamera

Annak érdekében, hogy a helyileg és távolról megjelenített tartalmak zökkenőmentesen összeállíthatók legyenek, a különböző kamera-tulajdonságokat szinkronban kell tartani a kiszolgáló és az ügyfél között. A kamera átalakítása és kivetítése. A helyileg megjelenített tartalomnak például ugyanazt a kamera-átalakítást és-vetítést kell használnia, mint a legújabb távoli keret.

![Helyi és távoli kamera](./media/camera.png)

A fenti képen a helyi kamera a kiszolgáló által eljuttatott távoli kerethez képest átkerült. Ennek eredményeképpen a helyi tartalmat a távoli kerettől megegyező perspektívában kell megjeleníteni, és végül az eredményül kapott képet a helyi kamera területére kell felvenni, amely részletesen a [késői fázisok újravetítésének](late-stage-reprojection.md)részleteit ismerteti.

A távoli és a helyi megjelenítés közötti késleltetés azt jelenti, hogy a beállítások bármilyen módosítása késéssel történik. Ezért az új értékek nem használhatók azonnal ugyanazt a keretet.

A közeli és a távoli sík távolságok a kamera beállításainál adhatók meg. A 2. HoloLens az átalakítási és a leképezési adathalmazt a hardver határozza meg. Az [asztali szimuláció](../../concepts/graphics-bindings.md)használatakor a rendszer a függvény bemenetén keresztül állítja be őket `Update` .

A módosított értékek helyileg is használhatók, amint az első távoli keret megérkezik a velük. A 2. HoloLens a rendereléshez használandó adatkészleteket a *HolographicFrame* a többi holografikus alkalmazáshoz hasonlóan nyújtja. Az [asztali szimuláció](../../concepts/graphics-bindings.md)a függvény kimenetén keresztül kéri le őket `Update` .

## <a name="camera-settings"></a>Kamera beállításai

A kamera beállításai a következő tulajdonságokkal módosíthatók:

**Közel és messzi síkja:**

Győződjön meg arról, hogy nem állíthatók be érvénytelen tartományok, a **NearPlane** és a **FarPlane** tulajdonság csak olvasható, és egy különálló függvény **SetNearAndFarPlane** létezik a tartomány módosításához. Ezeket az adatfájlokat a rendszer a keret végén küldi el a kiszolgálónak.

> [!IMPORTANT]
> Az egységben ez automatikusan, a fő kamera közelében és a távoli síkok módosításakor lesz kezelve.

**EnableDepth**:

Néha hasznos lehet letiltani a távoli rendszerkép pufferének a hibakeresési célokra való írását. A mélység letiltásával leállíthatja a kiszolgálót a mélységi adatok elküldéséről is, így csökkentve a sávszélességet.

> [!TIP]
> Az egységben egy **EnableDepthComponent** nevű hibakeresési összetevő van megadva, amely a funkció a szerkesztő felhasználói felületen való váltására használható.

A kamera beállításainak módosítása a következőképpen végezhető el:

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
}
```

## <a name="api-documentation"></a>API-dokumentáció

* [C# CameraSettings](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](https://docs.microsoft.com/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11. Update függvény](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11:: Update függvény](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>További lépések

* [Grafikus kötés](../../concepts/graphics-bindings.md)
* [Újravetítés késői fázisban](late-stage-reprojection.md)
