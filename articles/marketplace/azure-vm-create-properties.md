---
title: Virtuálisgép-ajánlat tulajdonságainak konfigurálása az Azure Marketplace-en
description: Ismerje meg, hogyan konfigurálhatja a virtuális gépek ajánlatának tulajdonságait az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 30e5ea4df3de0b8a10c9c1f3b4b27a9e0a19359e
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491205"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>A virtuális gépek ajánlati tulajdonságainak konfigurálása

A **Tulajdonságok** lapon megadhatja azokat a kategóriákat, amelyek segítségével csoportosíthatja a virtuális gép (VM) ajánlatát az Azure Marketplace-en, az alkalmazás verzióját, valamint az ajánlatot támogató jogi szerződéseket.

## <a name="select-a-category"></a>Kategória kiválasztása

Válassza ki a kategóriákat és alkategóriákat, hogy az ajánlatot a megfelelő Azure Marketplace-beli keresési területeken helyezze el. Ügyeljen arra, hogy az ajánlata milyen módon támogassa ezeket a kategóriákat az ajánlat leírásában. A következők szerint válasszon:

- Legalább egy és legfeljebb két kategória, beleértve az elsődleges és a másodlagos kategóriát is (opcionális).
- Legfeljebb két alkategória lehet minden elsődleges és/vagy másodlagos kategóriához. Ha nem alkalmazható alkategória az ajánlatra, válassza a **nem alkalmazható** lehetőséget.

Tekintse meg a kategóriák és alkategóriák teljes listáját az [ajánlatban az ajánlott eljárásokat felsorolva](gtm-offer-listing-best-practices.md). A virtuális gép az Azure Marketplace-en mindig a **számítási** kategóriában jelenik meg.

## <a name="provide-terms-and-conditions"></a>Feltételek és kikötések megadása

A **jogi** területen adja meg az ajánlat használati feltételeit. Erre két lehetősége van:

- [A standard szerződés használata opcionális módosításokkal](#use-the-standard-contract)
- [Saját használati feltételek használata](#use-your-own-terms-and-conditions)

A standard szerződéssel és a választható módosításokkal kapcsolatos további információkért lásd: [standard szintű szerződés a Microsoft kereskedelmi piactéren](standard-contract.md). Letöltheti a [normál szerződés](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-fájlját (Győződjön meg róla, hogy az előugró ablakok blokkolása ki van kapcsolva).

### <a name="use-the-standard-contract"></a>A standard szintű szerződés használata

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft szabványos szerződést kínál, amely a kereskedelmi piactéren elérhető ajánlatokhoz használható. Ha a standard szintű szerződés keretében nyújtja a szoftverét, az ügyfeleknek csak egyszer kell elolvasniuk és elfogadniuk, és nem kell egyéni használati feltételeket létrehozniuk.

1. Jelölje be a **szabványos szerződés használata a Microsoft kereskedelmi piactérhez** jelölőnégyzetet.

   ![Bemutatja a Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződés használatát jelölőnégyzetet.](partner-center-portal/media/use-standard-contract.png)
1. A **megerősítő** párbeszédpanelen válassza az **elfogadás** lehetőséget. Előfordulhat, hogy fel kell görgetni, hogy láthassa.
1. A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

   > [!NOTE]
   > Miután közzétett egy ajánlatot a kereskedelmi piactérre vonatkozó standard szerződéssel, nem használhatja a saját egyéni használati feltételeit. Akár a standard szerződés keretében, opcionális módosításokkal vagy a saját használati feltételeinek megfelelően kínálja a megoldását.

### <a name="add-amendments-to-the-standard-contract-optional"></a>Módosítások hozzáadása a standard szerződéshez (nem kötelező)

Kétféle módosítás érhető el: *univerzális* és *Egyéni*.

#### <a name="add-universal-amendment-terms"></a>Univerzális módosítási feltételek hozzáadása

Az **univerzális módosítási feltételek a Microsoft kereskedelmi Marketplace-re vonatkozó szabványos szerződés** mezőjébe írja be az univerzális módosítási feltételeket. Ebben a mezőben korlátlan számú karaktert adhat meg. Ezek a feltételek a AppSource, az Azure Marketplace-en és/vagy Azure Portalban jelennek meg a felderítési és vásárlási folyamat során.

#### <a name="add-one-or-more-custom-amendments"></a>Adjon hozzá egy vagy több egyéni módosítást

1. A **Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződésének egyéni módosítások** területén válassza az **Egyéni módosítási időszak hozzáadása (maximum 10)** hivatkozást.
1. Az **Egyéni módosítási feltételek** mezőben adja meg a módosítási feltételeket.
1. A **bérlő azonosítója** mezőben adja meg a bérlő azonosítóját. Az ajánlat beszerzési folyamatában csak az egyéni feltételekhez megadott bérlői azonosítókkal rendelkező ügyfelek látják majd az Azure Portal.
   > [!TIP]
   > A bérlői azonosító azonosítja az ügyfelet az Azure-ban. Megkérheti az ügyfelet erre az azonosítóra, és megkeresheti a [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Tulajdonságok** lehetőséggel. A címtár-azonosító értéke a bérlő azonosítója (például: `50c464d3-4930-494c-963c-1e951d15360e` ). A szervezet bérlői AZONOSÍTÓját is megkeresheti a saját tartománynév URL-címével, amely a [Microsoft Azure és az Office 365-bérlői azonosítóját használja?](https://www.whatismytenantid.com/).
1. A **Leírás** mezőben opcionálisan megadhatja a bérlő azonosítójának rövid leírását. Ez a leírás segít azonosítani a módosítással megcélzott ügyfelet.
1. Másik bérlői azonosító hozzáadásához jelölje be az **ügyfél bérlői azonosítójának hozzáadása** hivatkozást, és ismételje meg a 3. és a 4. lépést. Legfeljebb 20 bérlői azonosítót adhat hozzá.
1. Egy másik módosítási kifejezés hozzáadásához ismételje meg az 1 – 5. lépést. Ajánlatunk legfeljebb tíz egyéni módosítási kifejezést biztosíthat. 
2. A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="use-your-own-terms-and-conditions"></a>Saját használati feltételek használata

A normál szerződés helyett saját használati feltételeket is megadhat. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

1. A **jogi** területen győződjön meg arról, hogy a **Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződés használata** jelölőnégyzet be van jelölve.
1. A **feltételek és kikötések** mezőben adja meg a legfeljebb 10 000 karakter hosszúságú szöveget.
1. A következő lapra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget, és adja meg a **listát**.

## <a name="next-steps"></a>További lépések

- [Virtuálisgép-ajánlatlista konfigurálása](azure-vm-create-listing.md)
