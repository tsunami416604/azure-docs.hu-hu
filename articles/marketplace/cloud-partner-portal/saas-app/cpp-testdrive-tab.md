---
title: Azure SaaS alkalmazás ajánlat teszt meghajtó konfigurációja |} A Microsoft Docs
description: Konfigurálja a SaaS-alkalmazás ajánlat kipróbálása az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: e97e79ef3077431d8fc627cd634a67b0a01b6f8e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993095"
---
# <a name="saas-application-test-drive-tab"></a>SaaS-alkalmazás Tesztverziós lapon

A Test Drive lap segítségével egy kipróbálására nyújt lehetőséget biztosítanak az ügyfelek számára.

## <a name="test-drive-benefits"></a>Test Drive előnyei

Egy próbaverziós megoldást vezet be az ügyfelek számára az ajánlott eljárás az, győződjön meg, hogy az általuk is megvásárolható magabiztosan. Próbaverziós lehetőségekről Test Drive az Érdeklődők generálása kiváló minőségű, a leghatékonyabb, és ezek nagyobb átalakítás vezet.

Próbálja ki a termék főbb funkciók és előnyök, mutatja be egy való életből vett megvalósítási forgatókönyv gyakorlati, önálló irányítású próbaverzióra rendelkező ügyfelek biztosít.

## <a name="how-a-test-drive-works"></a>Próbálja ki az működése

A potenciális ügyfelek keres, és felderíti az alkalmazás a Marketplace-en. Az ügyfél jelentkezik be, és elfogadja a használati feltételeket. Ezen a ponton az ügyfél megkapja az előre konfigurált környezet rögzített számú óra, próbálja ki a szerepeltetendő magas minősített érdeklődő kap közben. További információkért lásd: [Mi a Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="publishing-steps"></a>Közzétételi lépéseket

A test Drive hozzáadásának a fő közzétételi lépései a következők:

1. A Test Drive forgatókönyv meghatározása
2. Hozhat létre és/vagy a Resource Manager-sablon módosítása
3. A Test Drive részletes manuális létrehozása
4. Tegye közzé újra az ajánlatot

## <a name="setting-up-a-test-drive"></a>A test drive beállítása

Nincsenek elérhető Tesztverzió négy különböző típusú, minden egyes termék, a forgatókönyv és a piactér típusa alapján.

|  **Típus**          |  **Leírás**  |  **Telepítési utasítások**  |
|  ---------------   |  ---------------  |  ---------------  |
|     Azure Resource Manager               |    Egy Azure Resource Manager Test Drive egy központi telepítési sablont, amely tartalmazza az összes Azure-erőforrást, amely tartalmazza a megoldás a közzétevő létrehozása folyamatban. Termékek, amelyek illeszkednek a Test Drive az ilyen típusú, amelyekről csak Azure-erőforrások használatához.               |       [Azure Resource Manager – Tesztverzió](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)            |
|       Szolgáltatott             |       Egy üzemeltetett Test Drive megszűnnek a telepítő által a Microsoft üzemeltetési és karbantartását hajtja végre a Test Drive felhasználói kiépítésének és megszüntetésének biztosítása.             |         [Szolgáltatott tesztverzió](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)          |
|      Logikai alkalmazás              |       A logikai alkalmazás Tesztverziós olyan központi telepítési sablont, amely magában foglalja az összes összetett megoldások létrehozására szolgáló architektúrák hivatott. Az összes Dynamics-alkalmazások vagy egyéni termékek Test Drive az ilyen típusú kell használnia.            |      [Logikai alkalmazás kipróbálása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)             |
|       Power BI             |         A Power BI Test Drive egy személyre szabott irányítópult mutató beágyazott hivatkozást tartalmaz. Minden olyan terméket, amely egy interaktív Power BI vizuális kell használnia a Test Drive az ilyen típusú bemutatásához szeretne. A feltöltendő szüksége a Power BI embedded URL-CÍMÉT.          |        [Power BI Test Drive](#Power-BI-test-drive)           |

### <a name="power-bi-test-drive"></a>A Power BI kipróbálása

A következő lépések segítségével konfigurálhatja a tesztverziós.

1. Új ajánlat, válassza a **Test Drive**.
2. A Test Drive, jelölje be **Igen**.

   ![Teszt meghajtó engedélyezése](./media/saas-enable-test-drive.png)

   Ha engedélyezi a tesztverziós, látni fogja, a részletek és a műszaki konfiguráció képernyők, amelyek a következő képernyőfelvételen látható.

   ![Test drive konfigurációs űrlapján](./media/saas-test-drive-yes.png)

3. A **részletek**, adjon meg információt a következő mezőket:
  
   - Leírás – a test drive és a felhasználók mire használhatják azt írja le. Alapszintű HTML-címkék használatával formázhatja a leírás.
   - Felhasználói útmutatója –, amelyet az ügyfelek használhat, ha azok még tart a test drive felhasználói kézikönyv dokumentum feltöltése. A jelen kell lennie egy pdf-fájlt.
   - Test Drive bemutató videó (nem kötelező) – megadhat egy videót (YouTube vagy Vimeo) és a Test Drive léptethetők tekintse meg az ügyfelek számára. Adja meg a Videó URL-CÍMÉT.

4. A **műszaki konfigurációs**, adjon meg információt a következő mezőket:

   - Test Drive – kiválasztása típusú **Power BI** a legördülő listából.
   - A Power BI-irányítópultra mutató hivatkozás – adjon meg egy hivatkozást az irányítópulton.

5. A test drive konfigurálása után, válassza ki a **mentése**.


## <a name="next-steps"></a>További lépések

[Áruház részletei lap](./cpp-storefront-tab.md)