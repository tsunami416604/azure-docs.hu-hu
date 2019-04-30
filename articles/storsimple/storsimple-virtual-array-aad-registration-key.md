---
title: A StorSimple Virtual Arrayt új hitelesítési |} A Microsoft Docs
description: A szolgáltatás az AAD-alapú hitelesítést használ, hozzon létre új regisztrációs kulcsot, és az eszközök manuális regisztrációjához módját ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: alkohli
ms.openlocfilehash: 080f49ca1078858462264f229e9acfee6fad17d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61387660"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>A StorSimple az új hitelesítés használatára

## <a name="overview"></a>Áttekintés

A StorSimple-Eszközkezelő szolgáltatás a Microsoft Azure-ban fut, és több StorSimple Virtual Arrayt csatlakozik. Dátum, a StorSimple-Eszközkezelő szolgáltatás rendelkezik egy Access Control service (ACS) hitelesítésére használja a szolgáltatást, hogy a StorSimple-eszköz. Az ACS-mechanizmus hamarosan elavulttá fog, és a rendszer váltotta fel az Azure Active Directory (AAD) hitelesítéshez.

Ebben a cikkben található információk csak mindkét StorSimple 1200-as sorozat virtuális tömb csak alkalmazható. Ez a cikk ismerteti az AAD-hitelesítés és a kapcsolódó új Szolgáltatásregisztrációs kulcs és a StorSimple-eszközökre vonatkozik, mint a tűzfalszabályok módosításai részleteit.

Az AAD-hitelesítéssel történik, a StorSimple Virtual Arrayt (1200-as modell) 1 vagy újabb frissítést futtató.

Az AAD-hitelesítés bevezetésével miatt bekövetkező változások:

- URL-mintákra tűzfalszabályokat.
- Szolgáltatásregisztrációs kulcs.

Az alábbi szakaszok részletesebben ismertetik ezeket a módosításokat.

## <a name="url-changes-for-aad-authentication"></a>AAD-hitelesítés URL-cím módosításai

Győződjön meg arról, hogy a szolgáltatás használja-e az AAD-alapú hitelesítés, a minden felhasználó tartalmaznia kell az új hitelesítési URL-címeket be a tűzfalszabályok.

A StorSimple Virtual Array használata esetén győződjön meg arról, hogy a következő URL-cím szerepel-e a tűzfalszabályok:

| Az URL-minta                         | Felhő | Component/Functionality         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |AAD-hitelesítési szolgáltatás      |
| `https://login.microsoftonline.us` | Az USA kormányzata |AAD-hitelesítési szolgáltatás      |

Az URL-cím teljes listáját a StorSimple Virtual Arrayt mintáit, Ugrás [URL-mintákra vonatkozó tűzfalszabályok](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Ha a hitelesítési URL-cím nem szerepel a tűzfalszabályok túli elavulásának dátuma, a kritikus riasztás, amely a StorSimple-eszköz nem tudta hitelesíteni a szolgáltatással láthatják. A szolgáltatás nem tud kommunikálni az eszközön. A felhasználó ezt a riasztást lát, szükség esetén az új hitelesítési URL-címét. A riasztás további információért látogasson el [riasztások figyelése a StorSimple-eszköz használata](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Eszköz verziója és a hitelesítési módosítások

A StorSimple Virtual Array használata esetén a következő táblázat segítségével határozza meg, milyen lépéseket kell tennie az eszköz szoftververzióján futtatja alapján.

| Ha az eszköz fut.  | A következő művelet elvégzése                                    |
|----------------------------|--------------------------------------------------------------|
| 1.0-s vagy újabb frissítési és offline állapotban van. <br> Megjelenik egy értesítés, hogy az URL-je nem szerepel az engedélyezési listán.| 1. Módosítsa a tűzfal-szabályokat, a hitelesítési URL-címét. Lásd: [hitelesítési URL-címek](#url-changes-for-aad-authentication). <br> 2. [Az AAD-regisztrációs kulcs beolvasása a szolgáltatástól](#aad-based-registration-keys). <br> 3. 1 – 5. lépések végrehajtásával [csatlakozhat, a Windows PowerShell felületét a virtuális tömb](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Használat `Invoke-HcsReRegister` parancsmagot, hogy regisztrálja az eszközt a Windows Powershellen keresztül. Adja meg az előző lépésben kapott kulcsot.|
| 1.0-s vagy újabb frissítés és az eszköz online állapotban.| Nincs szükség műveletre.                                       |
| Frissítés a 0.6-os vagy korábbi és az eszköz offline állapotban. | 1. [Töltse le az 1.0-ás frissítés keresztül katalóguskiszolgáló](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [1.0-ás frissítés alkalmazza a helyi webes felhasználói felületen keresztül](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Az AAD-regisztrációs kulcs beolvasása a szolgáltatástól](#aad-based-registration-keys). <br>4. 1 – 5. lépések végrehajtásával [csatlakozhat, a Windows PowerShell felületét a virtuális tömb](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Használat `Invoke-HcsReRegister` parancsmagot, hogy regisztrálja az eszközt a Windows Powershellen keresztül. Adja meg az előző lépésben kapott kulcsot.|
| Frissítés a 0.6-os vagy korábbi és az eszköz online állapotban | Módosítsa a tűzfal-szabályokat, a hitelesítési URL-címét.<br> 1.0-ás frissítés telepítése az Azure Portalon keresztül. |

## <a name="aad-based-registration-keys"></a>AAD-alapú regisztrációs kulcsokat

A StorSimple Virtual Arrayt, új kulcsokkal AAD-alapú regisztráció megkezdése az 1.0-ás frissítés. A regisztrációs kulcs használatával regisztrálja az eszközt a StorSimple-Eszközkezelő szolgáltatásban.

Az AAD szolgáltatás új regisztrációs kulcsok nem használható, ha a 0.6-os vagy újabb frissítést futtató StorSimple Virtual Arrayt használja. A szolgáltatás regisztrációs kulcsának újragenerálása kell. Ha újragenerálja a kulcsot, az új kulcs szolgál az ezt követő eszközök regisztrálásához. A régi kulcsot már nem érvényes.

- Az aad-ben új regisztrációs kulcs 3 nap után lejár.
- Az aad-ben regisztrációs kulcsokat munkahelyi csak a StorSimple 1200-as sorozat virtuális Tárolótömböket futó Update 1 vagy újabb. Az AAD kulcsot a StorSimple 8000 sorozatú eszköz az nem fog működni.
- Az AAD-regisztrációs kulcsok hosszabbak, mint a megfelelő ACS-regisztrációs kulcsokat.

A következő lépésekkel hozzon létre egy AAD szolgáltatás regisztrációs kulcsát.

#### <a name="to-generate-the-aad-service-registration-key"></a>Az AAD Szolgáltatásregisztrációs kulcs létrehozása

1. A **StorSimple-Eszközkezelő**, lépjen a **felügyeleti &gt;**  **kulcsok**.
    
    ![Nyissa meg a kulcsokhoz](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Kattintson a **kulcs generálása**.

    ![Kattintson a fiókkulcs újbóli létrehozása](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Másolja ki az új kulccsal. A régebbi kulcs már nem működik.

    ![Erősítse meg a fiókkulcs újbóli létrehozása](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>További lépések

* További információ a központi telepítése [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)
