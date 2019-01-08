---
title: Dynamics 365 Customer Engagement-alkalmazás ajánlat Test Drive lap – Azure Marketplace-en |} A Microsoft Docs
description: Hogyan konfigurálható a tesztverziós az egy Dynamics 365 Customer Engagement-alkalmazás ajánlat az appsource-ban Marketplace-en.
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/25/2018
ms.author: pbutlerm
ms.openlocfilehash: 664a2c6bfc4a73b7d792b71c4b81df54b05fcd74
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54083033"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Dynamics 365 Customer Engagement-alkalmazás Tesztverziós lap

Használja a **Test Drive** lap használatával létrehozhat egy próbaverziója az ügyfelek számára.  Az ajánlat főbb funkciók és előnyök, mutatja be egy való életből vett megvalósítási forgatókönyv gyakorlati, önálló irányítású próbaverzió ügyfelek biztosít.  Próbaverziós lehetőségekről Test Drive az Érdeklődők generálása kiváló minőségű, a leghatékonyabb, és ezek nagyobb átalakítás vezet.  További információkért lásd: [Mi a Test Drive?](../../cloud-partner-portal-orig/what-is-test-drive.md)

A Test Drive élményt, a Dynamics 365-alkalmazások automatikusan fut egy Microsoft által üzemeltetett megoldás.  További információkért lásd: [üzemeltetett Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/hosted-test-drive).

A Test Drive lap három lehetséges részből áll: **Próbálja ki a**, **részletek**, és **műszaki konfigurációs**.  Az utolsó két szakasz csak akkor jelenik meg, miután engedélyezte a Test Drive-funkciókat.  Egy csillag (*) hozzáfűzi a mező nevét jelzi, hogy ez szükséges. 


## <a name="test-drive-section"></a>Test Drive szakasz

Ez a funkció engedélyezéséhez válassza **Igen** való **engedélyezése egy Test Drive**.


## <a name="details-section"></a>Részletes adatait tartalmazó részben

Az alapvető Test Drive-információkat biztosít a **részletek** szakaszban.   

![Részletek szakaszának kipróbálása](./media/test-drive-tab-details.png)

A következő táblázat ismerteti a test drive, Dynamics 365 alkalmazás beállításához szükséges mezőket.

|      Mező                    |    Leírás                  |
|    ---------                  |  ---------------                |
|      Leírás              |   Ismerteti, mi a Test Drive teheti. Alapszintű HTML-címkék használatával formázhatja a leírás. Ha például &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;, és fejlécben.  |
|  Felhasználói kézikönyv                  |   Töltse fel, hogy az ügyfelek segítségével megtudhatja, hogyan Test Drive felhasználói manuális. Ez a dokumentum .pdf fájlnak kell lennie.              |
|  Test Drive – bemutató videó (nem kötelező) |  Megadhat egy, a Test Drive bemutató videó. Egy ügyfél is ebben a videóban előtt kipróbálás azokat. Adja meg a Videó URL-CÍMÉT a YouTube vagy Vimeo. Ha **+ hozzáadni a videót**, kérni fogja a következő információkat biztosítja:<ul><li>Name (Név)</li><li>URL-cím</li><li>Miniatűr (PNG formátumban, 533 x 324 képpont)</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>Technikai konfigurációs szakasz

Ebben a szakaszban biztosítja a test drive vonatkozó technikai részleteket.

![Részletek szakaszának kipróbálása](./media/test-drive-tab-tech-config.png)

Ha a mezők rendelkezik a következő célokra:

|      Mező                    |    Leírás                  |
|    ---------                  |  ---------------                |
| Test Drive típusa            | Válasszon **Microsoft üzemeltetett (Dynamics 365 for Customer Engagement)**.  |
| Max. egyidejű tesztverziók    | Egy aktív Test Drive time lekérdezhet egyidejű példányainak számát. Minden felhasználó fog felhasználni a Dynamics-licenc, amíg a Test Drive aktív, így biztosíthatja, hogy a felhasználók a Test Drive legalább ennyi Dynamics licenceket kell. Ajánlott érték 3 – 5.  |
| Tesztelje a meghajtó időtartama (óra)   | Órák maximális száma a felhasználó Test Drive-példány lesz aktív. Miután ez az időszak túllépése esetén a példány – a bérlőről figyelmeztetés megszüntetésről. Javasolt érték 2 – 24 óra alkalmazásához összetettségétől függően. A felhasználó mindig kérheti egy másik Test Drive, ha elfogy a ideje, és újra ki kell számítani.  |
| Példány URL-címe                  | URL-cím a Test Drive először nyitja meg. Ez általában a Dynamics 365-példány, amelyen az alkalmazás URL-CÍMÉT és mintaadatok telepítve.  |
| Az Azure AD-bérlő azonosítója            | A Dynamics 365-példány az Azure-bérlő GUID Azonosítóját. Lekérje ezt az értéket, jelentkezzen be az Azure Portalon, és keresse meg a **Azure Active Directory** > **tulajdonságok kiválasztása** > **másolja ki a címtár-azonosító**.  |
| Azure AD-alkalmazás azonosítója               | Az Azure AD-alkalmazás GUID azonosítója  |
| Az Azure AD alkalmazás-kulcs              | A titkos kulcsot az Azure AD alkalmazás, például: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` |
| Az Azure AD-bérlő neve          | Az Azure-bérlőhöz, a Dynamics 365-példány neve. Például használja a < tenantname. > onmicrosoft.com, formátuma: `testdrive.onmicrosoft.com`  |
| Példány webes API URL-címe          | Webes API URL-címe a Dynamics 365-példány. Ezt az értéket lekérheti azokat a Microsoft Dynamics 365-példány, és ellenőrizheti, hogy **beállítások** > **testreszabási** > **fejlesztői Erőforrások** > **webes API-t (az URL-cím másolása) példány**. Példaérték: `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| Szerepkörnév                     | Az egyéni Dynamics 365-biztonsági szerepkör a Test Drive hozott létre, és hozzá lesz rendelve a felhasználók futnak, például amikor `testdriveuser`. |
|  |  |

Miután megadta a szükséges adatokat, válassza ki a **mentése**.


## <a name="next-steps"></a>További lépések

Ezután, a marketing és értékesítés információkkal szolgálnak a [kirakat Részletek lap](./cpp-storefront-details-tab.md).

