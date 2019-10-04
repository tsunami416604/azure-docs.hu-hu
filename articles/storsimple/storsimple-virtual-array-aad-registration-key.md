---
title: Új hitelesítés a StorSimple virtuális tömbökhöz | Microsoft Docs
description: Ismerteti, hogyan használható az HRE-alapú hitelesítés a szolgáltatáshoz, új regisztrációs kulcs előállítása, valamint az eszközök manuális regisztrálásának elvégzése.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 723d5e969ba2f635724ffa50d562a7abaf936dcf
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517139"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Az új hitelesítés használata a StorSimple

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

A StorSimple Eszközkezelő szolgáltatás Microsoft Azure fut, és több StorSimple virtuális tömbhöz csatlakozik. A mai napig a StorSimple Eszközkezelő Service egy Access Control szolgáltatást (ACS) használt a szolgáltatás hitelesítéséhez a StorSimple-eszközön. Az ACS-mechanizmus hamarosan elavult, és egy Azure Active Directory (HRE) hitelesítéssel lesz lecserélve.

A cikkben szereplő információk csak a StorSimple 1200 sorozatú virtuális tömbökre érvényesek. Ez a cikk ismerteti a HRE-hitelesítés és a kapcsolódó új szolgáltatás regisztrációs kulcsának részleteit, valamint a tűzfalszabályok StorSimple-eszközökre vonatkozó módosításait.

A HRE-hitelesítés a StorSimple virtuális tömbökben (1200-es modell) fut az 1. vagy újabb frissítést futtatva.

A HRE-hitelesítés bevezetése miatt a következő változások történnek:

- Tűzfalszabályok URL-mintái
- Szolgáltatás regisztrációs kulcsa.

Ezeket a módosításokat a következő szakaszokban részletesen ismertetjük.

## <a name="url-changes-for-aad-authentication"></a>HRE-hitelesítés URL-változásai

Annak biztosítása érdekében, hogy a szolgáltatás HRE-alapú hitelesítést használjon, az összes felhasználónak tartalmaznia kell az új hitelesítési URL-címeket a tűzfalszabályok között.

StorSimple virtuális tömb használata esetén győződjön meg arról, hogy a tűzfalszabályok a következő URL-címet tartalmazzák:

| URL-minta                         | Felhő | Összetevő/funkció         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |HRE hitelesítési szolgáltatás      |
| `https://login.microsoftonline.us` | Az USA kormányzata |HRE hitelesítési szolgáltatás      |

A StorSimple virtuális tömbök URL-mintáinak teljes listájáért keresse fel a [Tűzfalszabályok URL-mintáit](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Ha a tűzfalszabályok nem tartalmazzák a hitelesítési URL-címet az elavultság dátuma után, a felhasználók kritikus riasztást látnak arról, hogy a StorSimple-eszközük nem tudta hitelesíteni a szolgáltatást. A szolgáltatás nem fog tudni kommunikálni az eszközzel. Ha a felhasználók ezt a riasztást látják, fel kell venniük az új hitelesítési URL-címet. A riasztással kapcsolatos további információkért nyissa meg a [riasztások használata a StorSimple-eszköz figyeléséhez](storsimple-virtual-array-manage-alerts.md#networking-alerts)című témakört.

## <a name="device-version-and-authentication-changes"></a>Az eszköz verziószáma és a hitelesítés módosítása

Ha StorSimple virtuális tömböt használ, a következő táblázat segítségével határozza meg, hogy milyen műveletet kell végrehajtania a futtatott eszköz szoftverének verziójától függően.

| Ha az eszköz fut  | Végezze el a következő műveletet                                    |
|----------------------------|--------------------------------------------------------------|
| A 1,0-es vagy újabb frissítés offline állapotú. <br> Megjelenik egy riasztás arról, hogy az URL-cím nem engedélyezett.| 1. A tűzfalszabályok módosításával adja meg a hitelesítési URL-címet. Lásd: [hitelesítési URL-címek](#url-changes-for-aad-authentication). <br> 2. [Szerezze be a HRE regisztrációs kulcsát a szolgáltatásból](#aad-based-registration-keys). <br> 3. Hajtsa végre a 1-5. lépést a [virtuális tömb Windows PowerShell](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor)-felületéhez való kapcsolódáshoz.<br> 4. A `Invoke-HcsReRegister` parancsmag használatával regisztrálja az eszközt a Windows PowerShell segítségével. Adja meg az előző lépésben kapott kulcsot.|
| Frissítse a 1,0-es vagy újabb verziót, és az eszköz online állapotú.| Nincs szükség felhasználói műveletre.                                       |
| Az 0,6-es vagy korábbi verzió frissítése, az eszköz offline állapotban van. | 1. [Töltse le a 1,0-es frissítést a Catalog Server használatával](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Alkalmazza a 1,0-es frissítést a helyi webes felületen keresztül](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Szerezze be a HRE regisztrációs kulcsát a szolgáltatásból](#aad-based-registration-keys). <br>4. Hajtsa végre a 1-5. lépést a [virtuális tömb Windows PowerShell](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor)-felületéhez való kapcsolódáshoz.<br>5. A `Invoke-HcsReRegister` parancsmag használatával regisztrálja az eszközt a Windows PowerShell segítségével. Adja meg az előző lépésben kapott kulcsot.|
| Az 0,6-es vagy korábbi frissítés, és az eszköz online állapotú | A tűzfalszabályok módosításával adja meg a hitelesítési URL-címet.<br> Telepítse a 1,0-es frissítést a Azure Portal. |

## <a name="aad-based-registration-keys"></a>HRE-alapú regisztrációs kulcsok

A StorSimple virtuális tömbök 1,0-es frissítésének megkezdése után új HRE-alapú regisztrációs kulcsok használatosak. A regisztrációs kulcsok használatával regisztrálja StorSimple Eszközkezelő szolgáltatását az eszközön.

Ha a 0,6-es vagy korábbi frissítést futtató StorSimple virtuális tömböket használ, nem használhatja az új HRE szolgáltatás regisztrációs kulcsait. Újra kell létrehoznia a szolgáltatás regisztrációs kulcsát. A kulcs újralétrehozása után az új kulcs az összes további eszköz regisztrálására szolgál. A régi kulcs már nem érvényes.

- Az új HRE regisztrációs kulcs 3 nap után lejár.
- A HRE-regisztrációs kulcsok csak az 1. vagy újabb frissítést futtató StorSimple 1200 sorozatú virtuális tömbökkel működnek. A StorSimple 8000 sorozatú eszköz HRE-regisztrációs kulcsa nem fog működni.
- A HRE regisztrációs kulcsa hosszabb a megfelelő ACS regisztrációs kulcsoknál.

A HRE szolgáltatás regisztrációs kulcsának létrehozásához hajtsa végre az alábbi lépéseket.

#### <a name="to-generate-the-aad-service-registration-key"></a>A HRE szolgáltatás regisztrációs kulcsának előállítása

1. A **StorSimple Eszközkezelő**lépjen a **felügyeleti &gt;**  **kulcsok**elemre.
    
    ![Ugrás a kulcsokra](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Kattintson a **kulcs**előállítása elemre.

    ![Kattintson az újragenerált elemre.](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Másolja az új kulcsot. A régebbi kulcs már nem működik.

    ![Újragenerált megerősítés megerősítése](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>További lépések

* További információ a [StorSimple virtuális tömb](storsimple-virtual-array-deploy1-portal-prep.md) üzembe helyezéséről
