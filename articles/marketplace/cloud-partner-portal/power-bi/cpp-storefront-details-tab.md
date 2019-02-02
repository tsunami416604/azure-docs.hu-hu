---
title: Ajánlat storefront adatait a Power BI alkalmazás – Azure Marketplace-en |} A Microsoft Docs
description: Egy Power BI alkalmazás ajánlat kirakat részletek mezők konfigurálása a Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 01/30/2019
ms.author: pbutlerm
ms.openlocfilehash: ffc0d8e9e41cf4d202b4040ce3f09e7180101c8b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666806"
---
# <a name="power-bi-apps-storefront-details-tab"></a>A Power BI alkalmazások kirakat Részletek lap

Használja a **kirakat részletek** lapján a **új ajánlat** lap marketinges, értékesítési és jogi információt nyújt a leendő ügyfelek számára. Ezen a lapon is megadja, hogyan kezelheti a marketplace-ről új potenciális ügyfeleket. A hosszú képernyőn hat szakaszokból áll: **Az ajánlat részletei**, **részleteket felsoroló**, **Marketing-összetevők**, **jogi**, **ügyfél-támogatási**, és **Felügyeleti vezethet**.  A hozzáfűzött csillag (*) a mező címke jelzi, hogy szükséges.


## <a name="offer-details-section"></a>Az ajánlat részletei szakasz

Ebben a szakaszban, az alkalmazás forrás ajánlat kapcsolatos általános információkat adja meg.

![Az ajánlat részletei szakaszban az kirakat részletei lapon](./media/offer-details-section.png)

A következő táblázat ismerteti a nevét, és ezek a mezők célját.

|   Mező               |   Leírás                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------|
| **Ajánlat-összefoglaló**     | Az alkalmazás rövid célját. A hosszabb 100 karakternél.                             |
| **Az ajánlat leírása** | Alkalmazás leírása. Legfeljebb 3000 karakter hosszúságú támogatja az egyszerű HTML-formázás. |
|                       |                                                                                         |


## <a name="listing-details-section"></a>Részletes adatait tartalmazó részben listázása

Ez a második szakasz további kontextust biztosít a az alkalmazás: milyen ágazatokban, általában használt, bevált melyik kategória vonatkoznak rá, kompatibilis termékek és kapcsolódó keresési feltételeket.

![Lista részletei szakaszban az kirakat részletei lapon](./media/listing-details-section.png)

 A következő táblázat ismerteti a nevét, és ezek a mezők célját.
 
|   Mező                                  |   Leírás                                                        |
| --------------                           | ---------------------                                                |
| **Ágazatok**                           | Válassza ki az iparág legjobb igazított az alkalmazást. Ha az alkalmazás több iparágban vonatkozik, is ezt üresen hagyja.      |
| **Kategóriák**                           | Válassza ki, amely az alkalmazás a kategóriák. Válasszon legfeljebb 3.     |
| **Az alkalmazáshoz Súgó hivatkozásra**               | Egy oldal, amely rendelkezik az alkalmazás online súgó URL-címe           |
| **Az alkalmazás működik együtt (max. 3) termékek** | A megadott termékek listáját, amelyek az alkalmazás működik együtt. Legfeljebb 3 termékek listázhatja. Termék listázásához, kattintson a a melletti plusz jelre (új), és a egy új nyílt szövegmező jön létre egy terméket, amely együttműködik az alkalmazás nevét adhatja meg.      |
| **A keresési kulcsszavak (max. 3)**              | Appsource-ban lehetővé teszi, hogy az ügyfél alapján kulcsszavak keresése. A kulcsszavak, amelynek az alkalmazás nem jelenik meg az ügyfelek halmaza is megadhatja. Például ha az alkalmazás "Küldés alkalmazásom" e-mailek, levelezési, Mail alkalmazásban lehet bizonyos kulcsszavakat. Válassza ki a szavak, amelyek a felhasználók valószínűleg használatával keres az alkalmazás az appsource-ban keresőmezőbe. |
|  |  |


## <a name="marketing-artifacts-section"></a>Marketing-összetevők szakasz

Ez a harmadik szakasz lehetővé teszi a védjegy és termékfényképek marketingcélú feltöltése.  Négy alszakaszok oszlik: **Emblémák**, **videók**, **dokumentumok**, és **képernyőképek**. Emblémák, képernyőfelvételekkel mutatja be az olyan szükséges marketing; azonban az összes különösen ajánlott legjobb ügyfél jogorvoslati.

![Marketing-összetevők szakaszban az áruház oldala részletei lapon](./media/marketing-artifacts-section.png)

 
|    Mező                             |    Leírás                                                    |
|   -----------                        |    -------------                                                  |
| *Emblémát*                              |                                                                   |
| **Az ajánlat embléma (png formátumú, 48 x 48)**   | Megjelenik az appsource-ban az alkalmazás vagy alkalmazás eredmények áttekintése a keresés befejezésekor. Png formátumú, 48px pontossággal\*48px használata támogatott.  |
| **Az ajánlat embléma (png formátumú, 216 x 216)** | Az alkalmazás Részletek lapján megjelenik az appsource-ban.  Png formátumú, 216px pontossággal\*216px használata támogatott.  |
| *Videók*                             |                                                                   |
| **Name (Név)**                             | Név vagy az alkalmazás címe                                          |
| **URL-cím**                              | YouTube vagy Vimeo található videó URL-címe                              |
| **Miniatűr**                        | Az alkalmazás miniatűr képét.  Csak png formátumú, 1280px pontossággal\*720px használata támogatott.   |
| *Dokumentumok*                          | Nem kötelező, de legfeljebb három dokumentumokat. Itt feltöltheti docs az appsource-ban "További információ" alatt jelenik meg.  |
| **Name (Név)**                             | Név vagy dokumentum támogató címe                              |
| **Fájl**                             | Töltse fel a dokumentum pdf formátumban kell megadni                             |
| *Képernyőképek*                        | Nem kötelező, de legfeljebb öt képernyőképeket.                        |
| **Name (Név)**                             | Neve vagy címe képernyőképe                                       |
| **Rendszerkép**                            | Töltse fel a képernyőre felvett lemezképet, 1280px felbontású png formátumúnak kell lenniük\*720px  | 
|   |   |


### <a name="logo-guidelines"></a>Emblémával kapcsolatos irányelvek

Feltöltött összes emblémát a [Cloud Partner Portalon](https://cloudpartner.azure.com) az irányelveket kell követniük:

- Ne használjon egy színátmenetes hátterének az embléma.
- Szöveg kerüli – beleértve a vállalata vagy márkanév – az embléma a. Az embléma megjelenését és működését "egyszerű" kell lennie, és kerülje átmenetekhez.
- Az embléma nem stretch.


## <a name="legal-section"></a>Jogi szakasz

Ez a negyedik szakasz lehetővé teszi, hogy a két jogi dokumentumok ajánlatok szükséges: Adatvédelmi szabályzat és a használati feltételeket.

![Jogi szakaszban az áruház oldala részletei lapon](./media/legal-section.png)

|   Mező                |   Leírás                           |
|------------------------|--------------------------------------   |
| **Adatvédelmi szabályzat URL-címe** | A közzétett adatvédelmi szabályzat URL-címe       |
| **Használati feltételek**       | Egyszerű szöveges vagy egyszerű HTML-házirend     |
|  |  |


## <a name="customer-support-section"></a>Ügyfél-támogatási szakasz

Adja meg a **támogatja az URL-cím** az online ügyfelek támogatási oldalán.  Emellett akkor ajánlott, ha ez az online támogatási oldal nyújt több kapcsolatfelvételi lehetőségek, például telefonon, e-mailek és élő Csevegésben ügyfeleit. 


## <a name="lead-management-section"></a>Elégtelen területén

Az utolsó szakaszban lehetővé teszi, hogy összegyűjtse az ügyfelek az appsource-ban ajánlatok az új potenciális ügyfeleket. A következő tárolási lehetőségek (legördülő listából) az érdeklődők adatait kínál.

|    Mező               |   Cél vezethet                               |
|------------------------|--------------------------------------            |
|  **Nincsenek**              | Érdeklődők nincsenek összegyűjtött (alapértelmezett).  |
| **Azure Blob (deprecated)** | Egy [az Azure blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview), azokat a tároló nevét és a egy kapcsolati karakterláncot.  Ez a választás elavult; használjon **Azure Table** helyette.  |
| **Azure-tábla**        | Egy [Azure-tábla](https://docs.microsoft.com/azure/cosmos-db/table-storage-overview), kapcsolati karakterlánc által meghatározott  |
| **Dynamics CRM Online** | A [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) példányhoz egy URL-cím és a hitelesítést a hitelesítő adatokat a megadott |
| **HTTPS-végpont**     | A JSON-adatként megadott HTTPS-végpont   |
| **Marketo**            | A [Marketo](https://www.marketo.com/) példány, a megadott kiszolgáló-Azonosítót, a munchkin azonosító és az űrlap azonosítója   |
| **Salesforce**         | A [Salesforce](https://www.salesforce.com/) adatbázis, az objektum azonosítója által megadott |
|  |  |

Miután közzéteszi az ajánlatot, az érdeklődő kapcsolat ellenőrzése, és egy tesztelési érdeklődő automatikusan elküld a megadott célpartícióra. Elégtelen információk folyamatosan kell kezelni, és ezek a beállítások azonnal frissíteni kell, hogy az ügyfél aktuális architektúra.


## <a name="next-steps"></a>További lépések

A következő [névjegyek](./cpp-contacts-tab.md) lap, Ön biztosítja műszaki és felhasználói támogatási információk az ajánlatban.
