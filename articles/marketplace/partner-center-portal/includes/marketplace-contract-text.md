---
title: fájl belefoglalása
description: szövegfájl belefoglalása a Microsoft kereskedelmi Marketplace standard szerződéshez
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: ChJenk
manager: evansma
editor: ''
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/22/2020
ms.author: v-chjen
ms.custom: include file
ms.openlocfilehash: 76603ef30cefa14018a7c6b976eb8541b9b608f4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76760893"
---
A Microsoft szabványos szerződési sablont biztosít a kereskedelmi piactérhez.

- **Szabványos szerződést használ a Microsoft kereskedelmi piactérről?**

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft szabványos szerződést biztosít a Microsoft kereskedelmi piactérről, amely megkönnyíti a tranzakciók megkönnyítése a piactéren. Az egyéni használati feltételek és kikötések elvégzése helyett a kereskedelmi piactér-közzétevők dönthetnek úgy, hogy a standard szintű szerződés keretében kínálják a szoftvereket, amelyeket csak egyszer kell bemutatni és elfogadni. A standard szintű szerződés itt található: https://go.microsoft.com/fwlink/?linkid=2041178.

A normál szerződést úgy is kiválaszthatja, hogy a saját használati feltételeinek megadása helyett a "szabványos szerződés használata a kereskedelmi piactérhez" jelölőnégyzetet válassza.

![A standard szerződés használata jelölőnégyzet](./media/use-standard-contract.png)

> [!NOTE]
> Miután közzétette az ajánlatot a Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződéssel, nem használhatja saját használati feltételeit és kikötéseit. Ez egy "vagy" forgatókönyv. A megoldást a standard szerződés **vagy** a saját használati feltételei alapján ajánljuk fel. Ha módosítani szeretné a standard szerződés feltételeit, ezt a standard szintű szerződés módosításain keresztül teheti meg.

**Standard szintű szerződés módosításai**

A standard szintű szerződések módosításai lehetővé teszik a kiadók számára, hogy a szokásos szerződési feltételeket használják az egyszerűség kedvéért, és testre szabják a termék vagy a vállalat használati Az ügyfeleknek csak akkor kell áttekinteniük a szerződés módosításait, ha már áttekintették és elfogadták a Microsoft standard szerződést.

A kereskedelmi piactér-közzétevők számára két fajta módosítás érhető el:

- Univerzális módosítások: ezeket a módosításokat a rendszer univerzálisan alkalmazza az összes ügyfélre vonatkozó standard szerződésre. Az univerzális módosítások az ajánlat minden ügyfelének a vásárlás folyamatában jelennek meg. Az ügyfeleknek el kell fogadniuk a standard szerződés és a módosítás feltételeit, mielőtt felhasználhatják az ajánlatot.
- Egyéni módosítások: ezek a módosítások a standard szerződés speciális módosításai, amelyek csak az Azure-bérlői azonosítók használatával vannak megcélozva az egyes ügyfelekre. A kiadók kiválaszthatják, hogy melyik bérlőt szeretnék megcélozni. Az ajánlat vásárlási folyamatában csak a bérlő ügyfelei jelennek meg az egyéni módosítási feltételekkel.  Az ügyfeleknek el kell fogadniuk a standard szerződés feltételeit és a módosítás (oka) t, mielőtt felhasználhatják az ajánlatot.

>[!NOTE]
> Ez a két típusú módosítás egymásra épül. Az egyéni módosításokkal rendelkező ügyfelek a vásárlás során általános módosítást is kapnak a standard szerződéshez.

**Általános módosítási feltételek a Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződéshez**: ebben a mezőben adja meg az univerzális módosítási feltételeket. Ajánlathoz egyetlen általános módosítást is megadhat. Ebben a mezőben korlátlan számú karaktert adhat meg. Ezek a feltételek a AppSource, az Azure Marketplace-en és/vagy Azure Portalban jelennek meg a felderítési és vásárlási folyamat során.

**Egyéni módosítási feltételek a Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződéshez**: először válassza az **Egyéni módosítási feltételek hozzáadása**lehetőséget. Ajánlatunk legfeljebb 10 egyéni módosítási feltételt adhat meg.

- **Egyéni módosítási feltételek**: az egyéni módosítási feltételek mezőben adja meg az egyéni módosítási feltételeket. Ebben a mezőben korlátlan számú karaktert adhat meg. Csak az egyéni feltételekhez megadott bérlői azonosítók ügyfelei jelennek meg az ajánlat vásárlási folyamatában az Azure Portalban.  
- **Bérlői azonosítók** (kötelező): minden egyéni módosítás legfeljebb 20 bérlői azonosítót célozhat meg. Ha egyéni módosítást ad hozzá, meg kell adnia legalább egy bérlői azonosítót. A bérlő azonosítója azonosítja az ügyfelet az Azure-ban. Megkérheti az ügyfelet erre az AZONOSÍTÓra, és megkeresheti a portal.azure.com > Azure Active Directory > a Tulajdonságok lehetőségre kattintva. A címtár-azonosító értéke a bérlő azonosítója (például 50c464d3-4930-494c-963c-1e951d15360e). A szervezet bérlői AZONOSÍTÓját is megkeresheti a saját tartománynév URL-címével, amely a [Microsoft Azure és az Office 365-bérlői azonosítóját használja?](https://www.whatismytenantid.com).
- **Leírás** (nem kötelező): opcionálisan megadhatja a bérlői azonosító rövid leírását, amely segít azonosítani a módosítással megcélzott ügyfelet.

**Feltételek és kikötések**

Ha meg szeretné adni saját használati feltételeit, megadhatja őket a feltételek és kikötések mezőben. Ebben a mezőben legfeljebb 10 000 karakter hosszúságú szöveget adhat meg. Ha a feltételek és kikötések további leírást igényelnek, adjon meg egy URL-hivatkozást ebbe a mezőbe, ahol a feltételek és kikötések megtalálhatók. Aktív hivatkozásként jelenik meg az ügyfelek számára.

Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni a mentést** .
Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni a mentést** .
