---
title: Az Azure Application ajánlat kipróbálása |} A Microsoft Docs
description: Hogyan konfigurálása az Azure-alkalmazás az ajánlat kipróbálása az Azure Marketplace-en.
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: d51c7330acd37d8a861c136ea85a2ff456eb264e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446486"
---
# <a name="azure-applications-test-drive-tab"></a>Az Azure-alkalmazások Test Drive lap

A Test Drive lap segítségével egy kipróbálására nyújt lehetőséget biztosítanak az ügyfelek számára.

## <a name="test-drive-benefits"></a>Test Drive előnyei

Egy próbaverziós megoldást vezet be az ügyfelek számára az ajánlott eljárás az, győződjön meg, hogy az általuk is megvásárolható magabiztosan. Próbaverziós lehetőségekről Test Drive az Érdeklődők generálása kiváló minőségű, a leghatékonyabb, és ezek nagyobb átalakítás vezet.

A termék főbb funkciók és előnyök, mutatja be egy való életből vett megvalósítási forgatókönyv gyakorlati, önálló irányítású próbaverzió ügyfelek biztosít.

## <a name="how-a-test-drive-works"></a>Próbálja ki az működése

A potenciális ügyfelek keres, és felderíti az alkalmazás a Marketplace-en. Az ügyfél jelentkezik be, és elfogadja a használati feltételeket. Ezen a ponton az ügyfél megkapja az előre konfigurált környezet rögzített számú óra, próbálja ki a szerepeltetendő magas minősített érdeklődő kap közben. További információkért lásd: [Mi a Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>A test drive beállítása

A következő lépések segítségével engedélyezheti és konfigurálhatja a tesztverziós.

### <a name="to-enable-a-test-drive"></a>Próbálja ki az engedélyezése:

1. A **új ajánlat**, jelölje be a **Test Drive** fülre.
2. A **Test Drive**válassza **Igen** a **engedélyezése egy Test Drive**.

   ![Egy teszt meghajtó engedélyezése](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>A test drive konfigurálása:

Miután engedélyezte a tesztverziós, meg fogjuk adja meg a test drive beállítása a következő űrlapok:
  
 - Részletek
 - Technikai konfiguráció
 - Tesztelés üzembe helyezési előfizetés részletei

A következő képernyőfelvétel-készítés a Test Drive-képernyők jeleníti meg. Egy csillag (*) hozzáfűzi a mező nevét jelzi, hogy ez szükséges. 

![A test drive konfigurálása](./media/managed-app-configure-testdrive.png)

A következő táblázat ismerteti a felügyelt alkalmazás a test drive beállításához szükséges mezőket.

|    **Mező**       |  **Leírás**  |
|  ---------------   |  ---------------  |
|      Leírás              |   Ismerteti, mi a Test Drive teheti. Alapszintű HTML-címkék használatával formázhatja a leírás. Ha például &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;, és fejlécben.                |
|    Felhasználói kézikönyv                |     Töltse fel, hogy az ügyfelek segítségével megtudhatja, hogyan Test Drive felhasználói manuális. Ez a dokumentum .pdf fájlnak kell lennie.              |
|         Test Drive – bemutató videó (nem kötelező)           |       Megadhat egy, a Test Drive bemutató videó. Egy ügyfél is ebben a videóban előtt kipróbálás azokat. Adja meg a Videó URL-CÍMÉT a YouTube vagy Vimeo. Ha **+ hozzáadni a videót**, kérni fogja a következő információkat biztosítja:<ul><li>Name (Név)</li><li>URL-cím</li><li>Miniatűr (PNG formátumban, 533 x 324 képpont)</li></ul>            |
|       Példányok             |        Hány példányt szeretne konfigurálni, milyen régió(k), és milyen gyors az ügyfelek beszerezheti a Test Drive. További információkért lásd: [közzététele egy Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
|       Tesztelje a meghajtó időtartama (óra)             |       Azon órák számát adja meg egy egész számot. Az engedélyezett tartomány pedig 1 és 999 közötti.            |
|        Teszt meghajtó ARM-sablon            |        Töltse fel az Azure Resource Manager-sablonok az alkalmazás egy tömörített (.zip) fájlt. További információkért lásd: [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).            |
|        Hozzáférési információk            |         Adja meg a hozzáférési adatokat, miután az ügyfél lekéri a Test Drive. Ha például egy URL-címet a test drive elérni, és jelentkezzen az adatokat. . Alapszintű HTML-címkék használatával formázhatja a leírás. Ha például &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;, és fejlécben.          |
|       Access Subscription Id             |       Ez hozzáférést biztosít az Azure-szolgáltatások és az Azure Portalon. Az előfizetés, ahol az erőforrás-használat jelentett, és services díjszabása. Ha már nincs külön Azure-előfizetés a Tesztverzió csak, hozzon létre egy előfizetést.             |
|          Az Azure AD-bérlő azonosítója          |        Adjon meg egy meglévő bérlőt az Azure Active Directoryban, vagy hozzon létre egy test drive bérlőt.           |
|         Azure AD App Id           |       Létrehozhat és regisztrálhat egy új alkalmazást. A Microsoft ezt az alkalmazást a Test Drive-példány műveletek végrehajtásához használja.            |
|          Azure AD App Key          |         Az alkalmazás hitelesítési kulcs létrehozásához, és illessze be ezt a mezőt.          |

Miután megadta a szükséges adatokat, válassza ki a **mentése** a test drive beállításának befejezéséhez.

## <a name="next-steps"></a>További lépések

[Marketplace lap](./cpp-marketplace-tab.md)