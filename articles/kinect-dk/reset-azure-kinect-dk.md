---
title: Az Azure Kinect DK alaphelyzetbe állítása
description: Az Azure Kinect DK-eszköz gyári lemezképre történő visszaállítását mutatja be
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, visszaállítás
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201898"
---
# <a name="reset-azure-kinect-dk"></a>Az Azure Kinect DK alaphelyzetbe állítása

Előfordulhat, hogy a gyári lemezkép szerinti alaphelyzetbe kell állítania az Azure Kinect DK-t (például ha egy belsővezérlőprogram-frissítés nem megfelelően lett telepítve).

1. Kapcsolja ki az Azure Kinect DK-t. Ehhez távolítsa el az USB-kábelt és a tápkábelt.
  ![A visszaállítási gombot takaró csavar helyét mutató ábra.](media/reset-azure-kinect-dk-diagram.png)
1. A visszaállítási gomb eléréséhez távolítsa el a háromlábú állvány rögzítőjén található csavart.
1. Csatlakoztassa újra a tápkábelt.
1. Szúrja be egy kiegyenesített gemkapocs végét az üres csavarfuratba a háromlábú állvány rögzítőjén.
1. A gemkapocs segítségével óvatosan nyomja le és tartsa lenyomva a visszaállítási gombot.
1. Miközben lenyomva tartja a visszaállítási gombot, csatlakoztassa újból az USB-kábelt.
1. Körülbelül 3 másodperc után a bekapcsolt állapotot jelző lámpa borostyánsárga színűre vált. Miután a lámpa színe megváltozott, engedje fel a visszaállítási gombot.  
   
   A gomb felengedése után a bekapcsolt állapotot jelző lámpa fehér és borostyánsárga színnel fog villogni, amíg az eszköz alaphelyzetbe áll. 
1. Várjon, amíg a lámpa folyamatos fehér színnel kezd el világítani.
1. Helyezze vissza a csavart a háromlábú állvány rögzítőjébe, a visszaállítási gomb fölé.
1. Az Azure Kinect Viewer segítségével ellenőrizze, hogy a belső vezérlőprogram visszaállítása megtörtént-e. Ehhez indítsa el az [Azure Kinect Viewert](azure-kinect-viewer.md), majd válassza **az eszköz belső vezérlőprogramja verzióinformációinak keresésére szolgáló funkciót** az Azure Kinect DK-n telepített belső vezérlőprogram verziójának megtekintéséhez.

Mindig gondoskodjon arról, hogy a legfrissebb belső vezérlőprogram legyen telepítve az eszközön. A belső vezérlőprogram legfrissebb verziójának letöltéséhez használja az Azure Kinect Firmware Tool eszközt. A belső vezérlőprogram állapotának ellenőrzéséről további információt [az eszköz belsővezérlőprogram-verziójának ellenőrzésével foglalkozó részben talál](azure-kinect-firmware-tool.md#check-device-firmware-version).

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Az Azure Kinect DK bemutatása](about-azure-kinect-dk.md)
- [Az Azure Kinect DK beállítása](set-up-azure-kinect-dk.md)
- [Az Azure Kinect DK hardverspecifikációi: Üzemeltetési környezet](hardware-specification.md#operating-environment)
- [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md)
- [Azure Kinect Viewer](azure-kinect-viewer.md)
- [Szinkronizálás több Azure Kinect DK-eszközön](multi-camera-sync.md)
