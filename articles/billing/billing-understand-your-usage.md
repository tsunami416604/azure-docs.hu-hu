---
title: Az Azure részletes használatának megértéséhez |} Microsoft Docs
description: Megtudhatja, hogyan Elolvastam és megértettem a részletes használati CSV Azure-előfizetése szakasza
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
ms.author: tonguyen
ms.openlocfilehash: 723e42d2bb2af09eb2236c3cbefeee33987ea45b
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060800"
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>A Microsoft Azure részletes használati költségek feltételek megismerése 

A részletes használati költségek CSV-fájlt az aktuális elszámolási időszak napi és mérési szintű használati díjak tartalmazza. 

A részletes használati fájl megtekinteni [beszerzéséről a számla és a napi használati adatok számlázási Azure](billing-download-azure-invoice-daily-usage-date.md).
Rendszerben elérhető megnyithat egy táblázat alkalmazásban vesszővel tagolt (.csv) formátumban. Ha elérhető két verzióját látja, töltse le a 2-es verzióját. Ez a legújabb fájl formátumát.

Használati díjak az összes **havi** költségek az előfizetést. A használati díjak ne vegye figyelembe, bármely kreditek és az esetleges kedvezményeket.

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Részletes feltételeket és a részletes használati fájl leírása

A következő szakaszok ismertetik a fontos fogalmakat tartalmazza a 2-es verziójának a részletes használati fájl is látható.

### <a name="statement"></a>Utasítás

A részletes használati CSV-fájl felső részén látható a szolgáltatások Számlázási hónap során használt. A következő táblázat a használati leírása itt látható.

| Időtartam | Leírás |
| --- | --- |
|Billing Period (Számlázási időszak) |A számlázott időszak, mikor használták a mérőszámok |
|Meter Category (Mérési kategória) |A legfelső szintű szolgáltatást a használati azonosító |
|Meter Sub-Category (Mérési alkategória) |Azure-szolgáltatások, amelyek hatással lehetnek a sebesség típusát határozza meg. |
|Meter Name (Mérés neve) |A mérő feldolgozottként mértékegysége azonosítja. |
|Meter Region (Mérési régió) |Az egyes szolgáltatások árú datacenter helye alapján adatközpont helye |
|SKU |Minden Azure mérő egyedi rendszerazonosító azonosítja. |
|Unit (Egység) |A szolgáltatás számlázási egységét jelöli, Ha például GB, óra, 10 000 s. |
|Consumed Quantity (Felhasznált mennyiség) |A számlázott időszak során használt mérő mennyisége |
|Included Quantity (Bennefoglalt mennyiség) |A mérő az aktuális elszámolási időszak díjmentesen részét képező mennyisége |
|Overage Quantity (Kereten túli mennyiség) |A felhasznált és a szereplő mennyiséggel közötti különbséget szemlélteti. Díjon számlázzuk, az ezt a mennyiséget. Az ajánlat nem tartalmaz mennyiséggel ajánlatokhoz használatalapú fizetés az összesített megegyezik a felhasznált mennyiség. |
|Within Commitment (Keretfelhasználás) |A mérési költségek vannak vonni a 6-os vagy 12 hónap ajánlatát rendelt előfizetési összegét mutatja. A mérési díjak időrendi sorrendben vannak kivonva. |
|Currency (Pénznem) |Az aktuális elszámolási időszak használt pénznem |
|Overage (Kereten túli díjak) |A mérési költségek, amelyek meghaladják a 6-os vagy 12 hónap ajánlatát rendelt előfizetési összegét mutatja |
|Commitment Rate (Keretkihasználtság) |A 6-os vagy 12 hónap ajánlat társított összes előfizetési mennyisége alapján előre arányát jeleníti meg |
|Rate (Egységár) |Most számítjuk fel számlázható egységenként száma másodpercenként |
|Érték |A Túlhasználati mennyiség oszlop arány oszlop eredménye látható. Ha a felhasznált mennyiség nem haladhatja meg a szereplő mennyiséget, akkor nem kell fizetni ebben az oszlopban. |

### <a name="daily-usage"></a>Napi forgalom

A CSV-fájlt a napi használat részén látható, amelyek hatással vannak a számlázási rangsorolja a használat részleteiről. A következő táblázat a használati leírása itt látható.

| Időtartam | Leírás |
| --- | --- |
|Usage Date (Használat dátuma) |A dátum a mérő használatakor |
|Meter Category (Mérési kategória) |Azonosítja a legfelső szintű szolgáltatást, amelynek használata tartozik |
|Meter ID (Mérési azonosító) |A számlázási használati ár használt számlázott mérő azonosítója |
|Meter Sub-Category (Mérési alkategória) |Az Azure szolgáltatás típusa, amelyek hatással lehetnek a sebesség meghatározása |
|Meter Name (Mérés neve) |A mérő feldolgozottként mértékegysége azonosítja. |
|Meter Region (Mérési régió) |Az egyes szolgáltatások árú datacenter helye alapján adatközpont helye |
|Unit (Egység) |Az egység a mérő az felszámított azonosítja. Ha például GB, óra, 10 000 s. |
|Consumed Quantity (Felhasznált mennyiség) |A mérő, amely felhasználta az adott nap összege |
|Resource Location (Erőforrás helye) |Az Adatközpont, amelyen fut a mérő azonosítja. |
|Consumed Service (Használt szolgáltatás) |A használt Azure platform szolgáltatás |
|Erőforráscsoport |Az erőforráscsoport, amelyben a telepített mérő futtatja. <br/><br/>További információk: [Azure Resource Manager overview](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) (Az Azure Resource Manager áttekintése). |
|Instance ID (Példányazonosító) | A mérő azonosítója. <br/><br/> Az azonosító a mérő létrehozásakor megadott nevét tartalmazza. Az erőforrás nevét, vagy a teljes erőforrás-azonosító. További információkért lásd: [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources). |
|Címkék | A mérő hozzárendelt címkét. Számlázási rekordcsoportjának címkék használata.<br/><br/>Címkék segítségével például költségek elosztása a részleg, amely a mérő használja. Szolgáltatások, amelyek támogatják a kibocsátás címkék a virtuális gépek, tárolási és hálózati szolgáltatásokat használó kiépítve a [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources). További információkért lásd: [címke az Azure-erőforrások rendszerezése](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Additional Info (További információ) |Szolgáltatással kapcsolatos metaadatok. Például egy kép típusa egy virtuális géphez. |
|Service Info 1 (1. szolgáltatási megjegyzés) |A projekt nevét, amely az előfizetéshez tartozik a szolgáltatás |
|Service Info 2 (2. szolgáltatási megjegyzés) |A hagyományos mezője, amely rögzíti az opcionális szolgáltatással kapcsolatos metaadatok |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Hogyan tehetem meg arról, hogy helyesen-e a részletes használati fájlban díjak?
Ha a részletes használati fájlokat, amelyeket meg szeretne további részleteket a járnak, [a Microsoft Azure a számlázási ismertetése.](./billing-understand-your-bill.md)

## <a name="external"></a>Mi a helyzet a külső szolgáltatási díjak?
Külső szolgáltatások (más néven piactér rendelések) szerezhetők független szolgáltatás, és külön vannak számlázva. A díjak nem jelennek meg a Azure számlán. További tudnivalókért lásd: [az Azure külső szolgáltatási díjak megismeréséhez](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?) a probléma elhárítva gyors eléréséhez.
