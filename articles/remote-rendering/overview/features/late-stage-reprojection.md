---
title: Késői szakaszújravetítés
description: Információk a késői szakasz újravetítése és hogyan kell használni.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680985"
---
# <a name="late-stage-reprojection"></a>Késői szakaszújravetítés

*A Late Stage Reprojection* (LSR) egy olyan hardverfunkció, amely segít stabilizálni a hologramokat, amikor a felhasználó mozog.

A statikus modelleknek vizuálisan meg kell őrizniük a helyzetüket, amikor megkerüli őket. Ha úgy tűnik, hogy instabil, ez a viselkedés utalhat lsr problémák. Ne feledje, hogy további dinamikus átalakítások, például animációk vagy robbanásnézetek elfedhetik ezt a viselkedést.

Két különböző LSR mód közül választhat, nevezetesen **a Planar LSR** vagy **a Depth LSR**között. Az, hogy melyik aktív, az határozza meg, hogy az ügyfélalkalmazás beküld-e egy mélységpuffert.

Mindkét LSR mód javítja a hologram stabilitását, bár megvannak a különböző korlátaik. Kezdje azzal, hogy megpróbálja a Mélység LSR-t, mivel vitathatatlanul jobb eredményeket ad a legtöbb esetben.

## <a name="choose-lsr-mode-in-unity"></a>LSR mód kiválasztása Unity-ben

Az Egység szerkesztőben nyissa meg *a Fájl > buildelési beállítások lapot.* Válassza a *lejátszó beállítások a* bal alsó sarokban, majd ellenőrizze a Player > *XR beállítások > virtual reality SDK-k > A Windows Mixed Reality,* hogy **engedélyezése mélység puffermegosztás** be van jelölve:

![Mélységpuffer-megosztás engedélyezve jelző](./media/unity-depth-buffer-sharing-enabled.png)

Ha ez az, akkor az alkalmazás a Mélység LSR-t fogja használni, ellenkező esetben a Planar LSR-t fogja használni.

## <a name="depth-lsr"></a>Mélység ISR

Ahhoz, hogy a Mélység LSR működjön, az ügyfélalkalmazásnak meg kell adnia egy érvényes mélységpuffert, amely tartalmazza az LSR során figyelembe veendő összes megfelelő geometriát.

A Mélység ISR megpróbálja stabilizálni a videokeretet a megadott mélységpuffer tartalma alapján. Ennek következtében a nem megjelenett tartalom, például az átlátszó objektumok nem módosíthatók az LSR-rel, és instabilitást és újravetítési összetevőket mutathatnak.

## <a name="planar-lsr"></a>SíkOs LSR

A Planar LSR nem rendelkezik képpontonkénti mélységadatokkal, ahogy a Mélység LSR is. Ehelyett újrakivetíti az összes tartalmat egy sík alapján, amelyet meg kell adnia az egyes kereteknek.

A Planar LSR újraprojekteket tervez a legjobban a mellékelt sík közelében lévő objektumokról. Minél távolabb van egy tárgy, annál instabilabbnak fog tűnni. Bár a Mélység LSR jobban támogatja az objektumok különböző mélységekben történő újravetítését, a Planar LSR jobban működhet a síkhoz igazító tartalomhoz.

### <a name="configure-planar-lsr-in-unity"></a>A Síklárd LSR-ének konfigurálása egységben

A sík paraméterek egy úgynevezett *élességpontból*származnak, amelyen keresztül minden keretet `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`meg kell adnia. A részleteket a [Unity Fókuszpont API-ban](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) találja. Ha nem állít be fókuszpontot, a tartalék ot választjuk. Azonban, hogy az automatikus tartalék gyakran vezet az optimálistól elmaradó eredményeket.

Az élességpontot saját maga is kiszámíthatja, bár érdemes lehet azt a távoli leképezési állomás által számított ra alapozza. Hívj, `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` hogy megkapd. Meg kell adnia egy koordinátakeretet, amelyben ki tudja fejezni az élességpontot. A legtöbb esetben csak az eredményt szeretné `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` megadni.

Általában mind az ügyfél, mind a gazdagép olyan tartalmat jelenít meg, amelyről a másik oldal nem tud, például az ügyfél felhasználói felületi elemeit. Ezért érdemes lehet a távoli élességpontot egy helyileg kiszámított ponttal kombinálni.

A két egymást követő képkockában kiszámított élességpontok egészen eltérőek lehetnek. Egyszerűen használja őket, mint-van vezethet hologramok úgy tűnik, hogy ugráló körül. A viselkedés elkerülése érdekében ajánlatos interpolálni az előző és az aktuális élességpontok at.

## <a name="next-steps"></a>További lépések

* [Kiszolgálóoldali teljesítménylekérdezések](performance-queries.md)
