---
title: A Power BI alkalmazásajánlat kirakatrészletei | Azure Piactér
description: A Microsoft AppSource Piactér Power BI-alkalmazásajánlatának kirakatrészletei mezőinek konfigurálása.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: f4fb5fba78cdea7996d006d34b2f07f3ac5e58fa
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981290"
---
# <a name="power-bi-app-storefront-details-tab"></a>A Power BI alkalmazás kirakatrészletei lapja

>[!Important]
>2020. április 13-tól megkezdjük a Power BI-alkalmazásajánlatok felügyeletének áthelyezését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Az áttelepített ajánlatok kezeléséhez kövesse a [Power BI-alkalmazások létrehozásának áttekintése](https://aka.ms/AzureCreatePBIServiceApp) című témakör utasításait.

Az **Új ajánlat** lapon a **Kirakat részletei** lapon marketing,értékesítési és jogi információkat adhat meg a leendő ügyfeleknek. Ezen a lapon az Azure Marketplace által létrehozott érdeklődők felügyeletét is beállíthatja. Ez a hosszú űrlap hat részből áll: **Ajánlat részletei**, **Listázás részletei**, Marketing **összetevők**, **Jogi**, **Ügyfélszolgálat**és **Érdeklődőkezelés**.  A mezőfelirat végén lévő csillag (*) azt jelenti, hogy a mező szükséges.


## <a name="offer-details-section"></a>Ajánlat részletei szakasz

Az **Ajánlat részletei** csoportban adja meg az AppSource-ajánlat általános adatait.

![A Kirakat részletei lap Ajánlat részletei szakasza](./media/offer-details-section.png)

Az alábbi táblázat további információt tartalmaz ezekről a mezőkről. A kötelező mezőket csillag (*) vádolja.  

|   Mező               |   Leírás                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------|
| **Ajánlat összegzése\***     | Az alkalmazás rövid célja, legfeljebb 100 karaktert használva.                             |
| **Ajánlat leírása\*** | Az alkalmazás leírása, legfeljebb 3000 karakterhasználatával. Ez a mező támogatja az egyszerű HTML-formázást. |
|   |    |


## <a name="listing-details-section"></a>A lista részletei szakasz

A **Hirdetés részletei** szakasz információkat gyűjt az alkalmazás környezetéről: azokról az iparágakról, ahol általában használják, az alkalmazáskategóriát, a kompatibilis termékeket és a kapcsolódó keresési kifejezéseket.

![A Kirakat részletei lap Részletek című szakaszának listázása](./media/listing-details-section.png)

A szakasz mezőinek kitöltéséhez használja az alábbi táblázatot.  A kötelező mezőket csillag (*) vádolja.
 
|   Mező                                  |   Leírás                                                        |
| --------------                           | ---------------------                                                |
| **Ágazatok**                           | Válassza ki azt az iparágat, ahol az alkalmazás a legjobban megfelel. Ha az alkalmazás több iparágra vonatkozik, hagyja üresen ezt a mezőt.      |
| **Kategóriák\***                           | Legfeljebb három, az alkalmazáshoz kapcsolódó kategóriát választhat ki.     |
| **Az alkalmazás súgóhivatkozása\***               | Adjon meg egy URL-címet egy olyan oldalhoz, amely online segítséget nyújt az alkalmazáshoz.           |
| **Az alkalmazás által (max. 3)** | Szövegmező létrehozásához jelölje ki az **Új** pluszjel lehetőséget. A mezőbe írja be annak a terméknek a nevét, amelyen az alkalmazás együttműködik. Legfeljebb három terméket soroljon fel.       |
| **Keresési kulcsszavak (max. 3)**              | Adjon meg legfeljebb három olyan kulcsszót, amelyet a felhasználók valószínűleg az alkalmazás AppSource-ban való kereséséhez használnak. Ha például az alkalmazást "E-mailező alkalmazásnak" nevezik, a kulcsszavak lehetnek **e-mailek,** **levelezés**és **levelezési alkalmazás.** |
|  |  |


## <a name="marketing-artifacts-section"></a>Marketingösszetevők szakasz

A **Marketingösszetevők** csoportban töltsön fel márkajelzési és marketinganyagokat az AppSource-on való megjelenítéshez.  Ez a rész négy alszakaszból áll: **Emblémák**, **Videók**, **Dokumentumok**és **Képernyőképek**. Az emblémák és a képernyőképek az egyetlen szükséges marketingösszetevők. De a legjobb ügyfél fellebbezés, javasoljuk, hozzá téve videók és dokumentumok is.

![A Marketingösszetevők csoport a Kirakat részletei lapon](./media/marketing-artifacts-section.png)

A szakasz mezőinek kitöltéséhez használja az alábbi táblázatot. A kötelező mezőket csillag (*) vádolja.
 
|    Mező                             |    Leírás                                                    |
|   -----------                        |    -------------                                                  |
| *Logók*                              |                                                                   |
| **Ajánlat embléma (png formátum, 48x48)\***   | Töltsön fel egy emblémát, amely megjelenik az alkalmazás áttekintésében vagy az alkalmazás keresési eredményei között. Az AppSource csak PNG formátumot támogat, 48 x 48 px felbontással.  |
| **Ajánlat embléma (png formátum, 216x216)\*** | Töltsön fel egy emblémát az alkalmazás részletes oldalán való megjelenítéshez.  Az AppSource csak PNG formátumot támogat, 216 x 216 px felbontással.  |
| *Videók*                             |                                                                   |
| **Név**                             | Adja meg az alkalmazás nevét vagy címét.                                          |
| **Url**                              | Add meg a YouTube-on vagy a Vimeo-n tárolt videó URL-címét.                              |
| **Miniatűr**                        | Adja meg az alkalmazás miniatűr képét.  Az AppSource csak PNG formátumot támogat, 1280 x 720 px felbontással.   |
| *Dokumentumok*                          | Legfeljebb három dokumentumot adhat hozzá az **Learn more** AppSource-on megjelenítendő Dokumentumok cím alatt.  |
| **Név**                             | Adja meg a bizonylat nevét vagy címét.                              |
| **Fájl**                             | PDF-fájl feltöltése.                             |
| *Képernyőképek\**                      | Legfeljebb öt képernyőképet adhat hozzá.                        |
| **Név**                             | Adja meg a képernyőkép nevét vagy címét.                                       |
| **Kép**                            | Png-képernyőkép feltöltése. Felbontásának 1280 x 720 px-nek kell lennie.  | 
|   |   |

A Cloud Partner [Portalportálra](https://cloudpartner.azure.com) feltöltött emblémák *nem:*

- Használjon színátmenetet. A logó kinézetének laposnak kell lennie.
- Adja meg a vállalat nevét, a márkanevet vagy más szöveget. 
- Nézd kinyújtott.

## <a name="legal-section"></a>Jogi szakasz

A **Jogi** szakaszban adja meg az egyes ajánlatokhoz szükséges két jogi dokumentumot: az adatvédelmi szabályzatot és a használati feltételeket.

![A Kirakat részletei lap Jogi szakasza](./media/legal-section.png)

A szakasz mezőinek kitöltéséhez használja az alábbi táblázatot:

|   Mező                |   Leírás                           |
|------------------------|--------------------------------------   |
| **Adatvédelmi irányelvek URL-címe\*** | A közzétett adatvédelmi szabályzat URL-címe       |
| **Használati feltételek\***       | Az Ön használati házirendje egyszerű szövegként vagy egyszerű HTML formátumban     |
|  |  |


## <a name="customer-support-section"></a>Ügyfélszolgálati szakasz

Az **Ügyfélszolgálat** szakaszban adja meg az online ügyfélszolgálati oldal **támogatási URL-címét.**  Ezen az oldalon több kapcsolatfelvételi lehetőséget kell biztosítania, például telefont, e-mailt és élő csevegést. 


## <a name="lead-management-section"></a>Érdeklődőkezelés szakasz

Az **Érdeklődőkezelés** szakaszban állítsa be a rendszert az AppSource által generált ügyfélérdeklődők összegyűjtésére. Az érdeklődők tárolási beállításainak kiválasztásához használja az alábbi táblázatot:

|    Mező               |   Érdeklődő rendeltetése                               |
|------------------------|--------------------------------------            |
|  **Nincs**              | Nem kell nyomokat gyűjteni. Ez az alapértelmezett beállítás.  |
| **Azure Blob (elavult)** | Adja meg [az Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) segítségével egy tároló nevét és egy kapcsolati karakterláncot.  Ez a választás elavult. Használja **inkább az Azure Table-t.**  |
| **Azure-tábla**        | Adja meg [az Azure Table storage](https://docs.microsoft.com/azure/cosmos-db/table-storage-overview) egy kapcsolati karakterlánc használatával.  |
| **Dynamics CRM Online** | Adja meg [a Dynamics 365-öt](https://dynamics.microsoft.com/) URL-cím és hitelesítési hitelesítő adatok használatával. |
| **HTTPS-végpont**     | Adja meg a HTTPS-végpontot egy JSON-tartalom használatával.   |
| **Marketo**            | Adjon meg egy [Marketo-példányt](https://www.marketo.com/) kiszolgálóazonosítóval, munchkin-azonosítóval és űrlapazonosítóval.   |
| **Salesforce**         | Adja meg [a Salesforce-ot](https://www.salesforce.com/) egy objektumazonosító használatával. |
|  |  |

Az ajánlat közzététele után az érdeklődőkapcsolat érvényesítésre kerül, és a rendszer automatikusan elküldi a tesztérdeklődőt a megadott célhelyre. Folyamatosan kezelheti az érdeklődők adatait, és azonnal frissítse a beállításokat, hogy azok tükrözzék az aktuális ügyfélkezelési architektúrát.


## <a name="next-steps"></a>További lépések

A [Névjegyalbum](./cpp-contacts-tab.md) lapon technikai és felhasználói támogatási erőforrásokat biztosíthat az ajánlathoz.
