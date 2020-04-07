---
title: Z elleni küzdelem mérséklése
description: Leírja a z-harci leletek enyhítésére szolgáló technikákat
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680452"
---
# <a name="z-fighting-mitigation"></a>Z elleni küzdelem mérséklése

Ha két felület átfedi egymást, nem világos, hogy melyiket kell a másik tetejére tenni. Az eredmény képpontonként is változik, ami nézetfüggő összetevőket eredményez. Következésképpen, amikor a kamera vagy a háló mozog, ezek a minták észrevehetően villognak. Ezt a leletet *z-fighting-nak*hívják. Ar és VR alkalmazások esetében a probléma fokozódik, mivel a fejre szerelt eszközök természetesen mindig mozognak. A nézők kényelmetlenségének megelőzése érdekében z-elleni kockázatcsökkentő funkció érhető el az Azure távoli renderelés.

## <a name="z-fighting-mitigation-modes"></a>Z elleni küzdelem mérséklési módok

|Helyzet                        | Eredmény                               |
|---------------------------------|:-------------------------------------|
|Rendszeres z-harc               |![Z-harc](./media/zfighting-0.png)|
|Z elleni mérséklés lehetővé tett    |![Z-harc](./media/zfighting-1.png)|
|A sakktábla kiemelése engedélyezve|![Z-harc](./media/zfighting-2.png)|

A következő kód lehetővé teszi a z elleni küzdelem enyhítését:

``` cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

> [!NOTE]
> Z-harci mérséklése egy globális beállítás, amely hatással van az összes renderelt sebben.

## <a name="reasons-for-z-fighting"></a>Okai z-harcok

Z-harc történik elsősorban két okból:

1. amikor a felületek nagyon távol vannak a fényképezőgéptől, mélységi értékeik pontossága lebomlik, és az értékek megkülönböztethetetlenné válnak
1. ha a hálófelület felületei fizikailag átfedik egymást

Az első probléma mindig megtörténhet, és nehéz megszüntetni. Ha ez történik az alkalmazásban, győződjön meg arról, hogy a *közeli sík* távolsága a *távoli sík* távolságához olyan alacsony, mint a gyakorlat. Például egy 0,01-es távolságban lévő közeli sík és az 1000-es távolságú távoli sík sokkal korábban hozza létre ezt a problémát, mint a közeli sík 0,1-nél, a távoli sík pedig a 20-as távolságban.

A második probléma a rosszul szerzőijog által készített tartalom mutatója. A való világban két tárgy nem lehet ugyanazon a helyen egy időben. Az alkalmazástól függően előfordulhat, hogy a felhasználók tudni szeretnék, hogy léteznek-e egymást átfedő felületek, és hol vannak.Depending on the application, users might want to know whether overlapping surfaces exist and where they are. Például egy olyan épület CAD-jelenete, amely egy valós építés alapja, nem tartalmazhat fizikailag lehetetlen felületi metszéspontokat. A szemrevételezés érdekében rendelkezésre áll a kiemelési mód, amely animált sakktábla-mintaként jeleníti meg a potenciális z-fighting-t.

## <a name="limitations"></a>Korlátozások

A feltéve, z-elleni mérséklésa a legjobb erőfeszítés. Nincs garancia arra, hogy eltávolítja az összes z-harcok. Is ez akarat gépiesen jobban szeret egy felszín felső másik. Így ha olyan felületekkel rendelkezik, amelyek túl közel vannak egymáshoz, előfordulhat, hogy a "rossz" felület a tetején köt ki. Gyakori problémaeset, amikor szöveget és egyéb matricákat alkalmaznak egy felületre. A z-harci mérséklése lehetővé tette, hogy ezek a részletek könnyen csak eltűnnek.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

* A z elleni küzdelem mérséklésének engedélyezése kevés vagy semmilyen teljesítményterhelést nem okoz.
* Ezen kívül lehetővé teszi a z-harci overlay nem jár egy nem triviális teljesítmény felső, bár változhat attól függően, hogy a jelenet.

## <a name="next-steps"></a>További lépések

* [Renderelési módok](../../concepts/rendering-modes.md)
* [Késői szakaszújravetítés](late-stage-reprojection.md)
