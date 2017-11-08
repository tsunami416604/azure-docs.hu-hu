---
title: "Cserélje le a StorSimple EBOD vezérlő |} Microsoft Docs"
description: "Távolítsa el, és cserélje le a StorSimple 8600 eszközön legalább az egyik EBOD tartományvezérlők ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 8cbfa507-1a56-4e24-99dd-7db9abd3b850
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: b6834e00fd225261296f92b463f9847deecffcdb
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Cserélje le az EBOD vezérlőhöz a StorSimple eszköz
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [cserélje le a StorSimple eszköz egy EBOD vezérlő](storsimple-8000-ebod-controller-replacement.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag ismerteti, hogyan cserélje le a hibás EBOD vezérlő modul a Microsoft Azure StorSimple eszközön. Egy EBOD vezérlő modul cseréjéhez kell:

* Távolítsa el a hibás EBOD vezérlő
* Egy új EBOD controller telepítése

Mielőtt elkezdené, vegye figyelembe a következőket:

* Üres EBOD modulok minden nem használt tárolóhelyek kell beszúrni. A ház nem cool megfelelően, ha a tárhely marad nyitva.
* A EBOD tartományvezérlő közbeni-cserélhető és eltávolítani vagy lecserélni. Ne távolítsa el egy hibás modul, amíg nem helyettesíti. A cseréjét kezdeményezésekor 10 percen belül kell befejezése.

> [!IMPORTANT]
> Távolítsa el vagy cserélje le a StorSimple összetevők közül bármelyik megkísérlése előtt győződjön meg arról, hogy tekintse át a [biztonsági ikon egyezmények](storsimple-safety.md#safety-icon-conventions) és egyéb [biztonsági óvintézkedéseket](storsimple-safety.md).
> 
> 

## <a name="remove-an-ebod-controller"></a>Távolítsa el az EBOD vezérlőhöz
A StorSimple eszköz sikertelen EBOD vezérlő moduljának cseréje, előtt ellenőrizze, hogy a többi EBOD vezérlő modul aktív, és fut. Az alábbi eljárást és táblázatot azt ismertetik, hogyan EBOD vezérlő modul eltávolítása.

#### <a name="to-remove-an-ebod-module"></a>Egy EBOD modul eltávolítása
1. Nyissa meg a klasszikus Azure portálon.
2. Navigáljon a **eszközök** > **karbantartási** > **hardverállapot**, és ellenőrizze, hogy az aktív EBOD tartományvezérlő LED állapota a modul zöld pedig a sikertelen EBOD vezérlő modul LED piros.
3. Keresse meg a sikertelen EBOD vezérlő modul hátulján az eszközön található.
4. Távolítsa el a kábelek, mielőtt kilépteti a EBOD modul kívül a rendszer a tartományvezérlő-hez a EBOD vezérlő modul.
5. Jegyezze fel a pontos SAS-port-vezérlőhöz csatlakoztatott EBOD vezérlő modul. Akkor le kell állítani a rendszer ezt a konfigurációt a EBOD modul cseréje után. 
   
   > [!NOTE]
   > Általában ez lesz a Port, amely van címkézve **működteti** az alábbi diagram szemlélteti.
   > 
   > 
   
    ![Csatlakozópanel a EBOD vezérlő](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **1. ábra** vissza a EBOD modul
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Tartalék LED |
   | 2 |Energiagazdálkodási LED |
   | 3 |SAS-összekötők |
   | 4 |SAS LED |
   | 5 |Csak a gyári használatra soros portokhoz |
   | 6 |Port (a gazdagép) |
   | 7 |B port (gazdagép kimenő) |
   | 8 |Port C (csak a gyári általi használatra) |

## <a name="install-a-new-ebod-controller"></a>Egy új EBOD controller telepítése
Az alábbi eljárást és táblázatot ismertetik a StorSimple eszköz egy EBOD vezérlő modul telepítése.

#### <a name="to-install-an-ebod-controller"></a>Egy EBOD tartományvezérlő telepítése
1. Ellenőrizze a EBOD eszközön való megosztása kárt, különösen az illesztőfelület-összekötő. Ha bármely elgörbülve ne telepítse az új EBOD tartományvezérlő.
2. A zárolás van életben nyitva csúsztassa be a modul be a ház mindaddig, amíg a zárolás van életben kódolása.
   
    ![EBOD controller telepítése](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **2. ábra** a EBOD tartományvezérlő-modul telepítése
3. Zárja be a zárolás. A kattintson kell hall, mivel a zárolás kapcsolatba lép.
   
    ![EBOD zárolás feloldása](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **3. ábra** a EBOD modul zárolás bezárása
4. Csatlakozzon újból a kábelek. Használja a pontos konfigurációját, hogy a csere előtt voltak. A következő ábra és táblázat kábel összekapcsolása vonatkozó további információért tekintse meg.
   
    ![Az energiagazdálkodási 4U eszköz bekábelezése](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **4. ábra**. Újracsatlakozás a kábelek.
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Elsődleges ház |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |A vezérlő 0 |
   | 5 |1. vezérlő |
   | 6 |EBOD vezérlő 0 |
   | 7 |1. EBOD vezérlő |
   | 8 |EBOD ház |
   | 9 |Áramelosztó egységekből |

## <a name="next-steps"></a>Következő lépések
További információ [StorSimple hardver összetevő cseréje](storsimple-hardware-component-replacement.md).

