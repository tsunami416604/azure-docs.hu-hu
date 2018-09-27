---
title: Az Azure részletes használatának megértéséhez |} A Microsoft Docs
description: Ismerje meg, hogyan olvashatók és érthetők azon részei, az Azure-előfizetéshez kapcsolódó részletes használati CSV
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: cwatson
ms.openlocfilehash: a037f4837be65eb7eac76977a3728c7357e09463
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394435"
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>A Microsoft Azure a részletes használati költségek feltételeinek értelmezése 

CSV formátumú részletes használati költségek a jelenlegi számlázási időszak napi és a mérőszám szintű használati díjakat tartalmazza. 

A részletes használati fájlok lekéréséhez lásd: [beszerzése az Azure számlázási és napi használati adatok](billing-download-azure-invoice-daily-usage-date.md).
Egy vesszővel tagolt (.csv) formátumú, amelyeket megnyithat egy számolótábla alkalmazásban érhető el. Ha két elérhető verziót lát, a 2. verziót töltse le. Ez a legújabb fájl formátumát.

A használati díjak az összes **havi** díjak egy adott előfizetés. A használati díjak ne vegye figyelembe bármely jóváírások és engedmények.

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Részletes használati és a részletes használati fájl leírása

A következő szakaszok ismertetik a részletes használati fájlok 2-es verziója látható a fontos fogalmakat.

### <a name="statement"></a>Utasítás

A részletes használati adatok CSV-fájl felső részén látható a szolgáltatások, a havi számlázási időszak során használt. A következő táblázat felsorolja a feltételeket és a jelen szakaszban bemutatott leírása.

| Időtartam | Leírás |
| --- | --- |
|Billing Period (Számlázási időszak) |A számlázási időszak, mikor használták a mérőszámok |
|Meter Category (Mérési kategória) |A felső szintű szolgáltatásnak a használati azonosítja. |
|Meter Sub-Category (Mérési alkategória) |Azure-szolgáltatás, amely a díjszámítást is érintheti típusát határozza meg. |
|Meter Name (Mérés neve) |A rendszer, elérheti az érték mértékegységét azonosítja. |
|Meter Region (Mérési régió) |Az adatközpontok elhelyezkedésétől is adatközpont elhelyezkedése alapján igénybe vett |
|SKU |Azonosítja az egyes Azure-mérőszámok egyedi rendszerazonosítója |
|Unit (Egység) |A szolgáltatás számlázási egységét jelöli, Például: GB, óra, 10 000 s. |
|Consumed Quantity (Felhasznált mennyiség) |Az elszámolási időszakban használt mérőszám mennyisége |
|Included Quantity (Bennefoglalt mennyiség) |A mérőszám, amely tartalmazza az aktuális elszámolási időszakban érvényes díjmentesen mennyisége |
|Overage Quantity (Kereten túli mennyiség) |A felhasznált mennyiség és a foglalt mennyiség közötti különbséget mutatja. Ez a mennyiség díjköteles. Használatalapú fizetés, amelyek nem foglalt mennyiség szolgáltatásegységeket Ez a mennyiség megegyezik a felhasznált mennyiség. |
|Within Commitment (Keretfelhasználás) |A mérőszám a mennyiségnek a 6 vagy 12 hónapos kötelezettségvállaláshoz összegből díjakat jeleníti meg. Fogyasztásmérő díjak időrendi sorrendben vannak kivonhatók. |
|Currency (Pénznem) |Használja az aktuális elszámolási időszakban érvényes pénznem |
|Overage (Kereten túli díjak) |A mérőszám díjat a kötelezettségvállalás összege a 6 vagy 12 hónapos kötelezettségvállaláshoz tartozó keretet túllépő jeleníti meg |
|Commitment Rate (Keretkihasználtság) |Megjeleníti a keretkihasználtság mértékében a teljes mennyiségnek a 6 vagy 12 hónapos kötelezettségvállaláshoz alapján |
|Rate (Egységár) |A sebesség díját a számlázható egység |
|Érték |A Rate oszlop Kerettúllépés mennyiség oszlop szorzása eredménye látható. A felhasznált mennyiség nem haladja meg a foglalt mennyiséget, ha ott nem költségekkel ebben az oszlopban. |

### <a name="daily-usage"></a>Napi forgalom

A CSV-fájlt a napi használati szakaszában látható használati információk találhatók a díjszabása nem módosul. A következő táblázat felsorolja a feltételeket és a jelen szakaszban bemutatott leírása.

| Időtartam | Leírás |
| --- | --- |
|Usage Date (Használat dátuma) |A dátum, amikor az az érték lett megadva |
|Meter Category (Mérési kategória) |Azonosítja a legfelső szintű szolgáltatás, amelynek használati adatait. |
|Meter ID (Mérési azonosító) |A kiszámlázott mérő azonosítója, amellyel számlázási használati díja |
|Meter Sub-Category (Mérési alkategória) |Azure-szolgáltatás, amely a díjszámítást is érintheti típusát határozza meg. |
|Meter Name (Mérés neve) |A rendszer, elérheti az érték mértékegységét azonosítja. |
|Meter Region (Mérési régió) |Az adatközpontok elhelyezkedésétől is adatközpont elhelyezkedése alapján igénybe vett |
|Unit (Egység) |A mérőszám a felszámított egységét. Például: GB, óra, 10 000 s. |
|Consumed Quantity (Felhasznált mennyiség) |Felhasználta az adott napra mérőszám mennyisége |
|Resource Location (Erőforrás helye) |Az Adatközpont, ahol mérőszám fut. |
|Consumed Service (Használt szolgáltatás) |Az Azure platformszolgáltatás, amely használt |
|Erőforráscsoport |Az erőforráscsoport, amelyben a telepített mérő futtatja. <br/><br/>További információk: [Azure Resource Manager overview](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) (Az Azure Resource Manager áttekintése). |
|Instance ID (Példányazonosító) | A mérőszám azonosítója. <br/><br/> Az azonosító a mérőszám létrehozásakor megadott nevét tartalmazza. Az erőforrás nevét, vagy teljesen minősített erőforrás-azonosítója. További információkért lásd: [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources). |
|Címkék | A mérőszám hozzárendelt címkét. Használjon címkéket, hogy a számlázási rekordok csoportosítására.<br/><br/>Címkék használatával például osztani a költségeket az a fogyasztásmérő használó részleg által. Címkék kibocsátását támogató szolgáltatások a virtuális gépek, tárolási és hálózati szolgáltatások használatával a [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources). További információkért lásd: [címkékkel rendelkező Azure-erőforrások rendszerezése](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Additional Info (További információ) |Szolgáltatásspecifikus metaadatok láthatók. Ha például egy lemezkép típusa egy virtuális gép. |
|Service Info 1 (1. szolgáltatási megjegyzés) |A projekt nevét, amely az előfizetéséhez tartozik a szolgáltatás |
|Service Info 2 (2. szolgáltatási megjegyzés) |Régi mező, amelyben a nem kötelező szolgáltatásspecifikus metaadatok láthatók |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Hogyan ellenőrizhetem, hogy, hogy helyesek-e a részletes használati fájlban kell fizetni?
Ha a részletes használati fájl, amelyet további részleteket a díj, lásd: [Microsoft Azure-hoz kapcsolódó számlák magyarázata.](./billing-understand-your-bill.md)

## <a name="external"></a>Mi a helyzet a külső szolgáltatási díjak?
Külső szolgáltatásokhoz (más néven a Marketplace-en rendelések) szolgáltatás független gyártók által biztosított, és külön számlázzuk. A díjak nem jelennek meg az Azure-számlán. További tudnivalókért lásd: [az Azure külső szolgáltatási díjak értelmezése](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>Segítség Forduljon az ügyfélszolgálathoz.
Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?) a probléma gyors megoldása érdekében.
