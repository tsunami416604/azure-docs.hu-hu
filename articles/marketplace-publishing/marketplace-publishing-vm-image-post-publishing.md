---
title: A virtuálisgép-lemezképet az Azure Marketplace-en kezelése |} A Microsoft Docs
description: Részletes útmutató az első közzététele után a virtuálisgép-lemezképet az Azure Marketplace-en kezelése
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
ms.openlocfilehash: d4c7dce1876e9838fe986aebb7e38a09e8a82baf
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252972"
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Az Azure piactéren virtuálisgép-ajánlat utáni üzemi útmutató
Ez a cikk bemutatja, hogyan frissíthet egy élő virtuálisgép-ajánlat az Azure piactéren. Végigvezeti egy vagy több új termékváltozatok ad egy meglévő ajánlatra. Azt is végigvezeti a folyamat egy élő virtuálisgép-ajánlat vagy Termékváltozat eltávolításával a piactérről.

Miután egy ajánlat/SKU elő van készítve a a [az Azure portal](http://portal.azure.com), nem módosítható a következő szövegmezők:

* **Ajánlat azonosítója**: az a közzétételi portálon lépjen a **virtuális gépek** , és válassza ki az ajánlatot. Kattintson a **Virtuálisgép-rendszerképek** > **ajánlat azonosítója**.
* **Termékváltozat azonosítója**: az a közzétételi portálon lépjen a **virtuális gépek** , és válassza ki az ajánlatot. Kattintson a **Termékváltozatok** > **adjon hozzá egy Termékváltozatot**.
* **Közzétevő Namespace**: az a közzétételi portálon lépjen a **virtuális gépek** > **forgatókönyv** > **ossza meg velünk a kapcsolatot kapcsolatban a vállalati**("Lépés 2 regisztrálni a vállalat" alatt található) > **közzétevő Namespace** > **Namespace**.

Az ajánlat/SKU szerepel-e után a [Marketplace](https://azure.microsoft.com/marketplace), nem módosítható a következő szövegmezők:

* **Ajánlat azonosítója**: az a közzétételi portálon lépjen a **virtuális gépek** , és válassza ki az ajánlatot. Kattintson a **Virtuálisgép-rendszerképek** > **ajánlat azonosítója**.
* **Termékváltozat azonosítója**: az a közzétételi portálon lépjen a **virtuális gépek** , és válassza ki az ajánlatot. Kattintson a **Termékváltozatok** > **adjon hozzá egy Termékváltozatot**.
* **Közzétevő Namespace**: az a közzétételi portálon lépjen a **virtuális gépek** > **forgatókönyv** > **ossza meg velünk a kapcsolatot kapcsolatban a vállalati**("Lépés 2 regisztrálása" alatt található) **közzétevő Namespace** > **Namespace**.
* **Portok**: az a közzétételi portálon lépjen a **virtuális gépek** , és válassza ki az ajánlatot. Kattintson a **Virtuálisgép-rendszerképek** > **nyitott portok**.
* **Díjszabás felsorolt termékváltozat(ok) árának meghatározását módosítása**
* **Számlázási modell módosítás felsorolt termékváltozat(ok) árának meghatározását**
* **A listán szereplő termékváltozat(ok) árának meghatározását régiók számlázási eltávolítása**
* **Az adatlemezek száma a listán termékváltozat(ok) árának meghatározását módosítása**

## <a name="update-the-technical-details-of-a-sku"></a>Technikai részleteiről azon a Termékváltozat frissítése
Új verzió hozzáadása a felsorolt termékváltozatra, és közzé az ajánlatot még, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).
2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **Virtuálisgép-rendszerképek** fülre.
4. Az a **termékváltozatok** területén keresse meg a frissíteni kívánt Termékváltozatot.
5. Adjon hozzá egy új verziószámot a termékváltozathoz, majd kattintson a **+** gombra. Az új verzió-X.Y.Z formátumban, ahol X, Y és Z egész számoknak kell lennie. Megváltozik a verzió csak növekményes kell lennie.
6. Az a **OS VHD URL-cím** mezőbe írja be a közös hozzáférésű jogosultságkód URI-t az operációs rendszer virtuális merevlemez létrehozása és a módosítások mentéséhez.

   > [!IMPORTANT]
   > Nem növelési és csökkentési az adatlemezek száma a listán szereplő termékváltozat. Ebben az esetben egy új Termékváltozatban létrehozásához szükséges. Részletes útmutatásért tekintse meg a szakasz [adjon hozzá egy új Termékváltozatot a felsorolt ajánlatnak](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Nyissa meg a **közzététel** fülre, majd **átmeneti üzembe**. Tesztelés az ajánlat átmeneti környezetben részletes útmutatásért lásd: [tesztelése a Virtuálisgép-ajánlat a Marketplace-en](marketplace-publishing-vm-image-test-in-staging.md).
8. Az átmeneti állapotában is az ajánlat tesztelését, követően nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.

    ![Virtuálisgép-rendszerképek](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Az ajánlat vagy a Termékváltozat felhasználóknak részleteinek frissítése
Frissítheti a felhasználóknak (marketing, jogi, támogatják, kategóriák) a Termékváltozat a Marketplace-en vagy élő ajánlat részletei.

### <a name="update-the-offer-description-and-logos"></a>Az ajánlat leírása és emblémák frissítése
Frissítése az ajánlat részleteit, és tegye közzé újra az ajánlatot, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).
2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **MARKETING** fülre.
4. Kattintson a **angol (US)**.
5. Kattintson a **részletek** fülre. Az a **leírás** területén frissíteni az ajánlatot **cím**, **összefoglalás**, és **hosszú összegzése** , és mentse a módosításokat.

   > [!NOTE]
   > Amikor frissíti a Termékváltozatának adatait, vegye figyelembe ezeket a korlátozásokat: 
   * Ismétlődő szöveg ne adja meg az ajánlat leírása és a Termékváltozat leírása.
   * Ne adja meg a Termékváltozat-cím és az ajánlat hosszú összegzése ismétlődő szöveget. 
   * Ne adja meg a Termékváltozat-cím és az ajánlat összefoglalásának ismétlődő szöveget.
   >
   >

    ![Részletek](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. Az a **emblémák** szakaszában a **részletek** fülre, frissítse az emblémát. Győződjön meg arról, hogy a emblémák kövesse a [Azure Marketplace-en irányelvek](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > Egy hero ikon nem kötelező. Ha szeretné, nem egy hero ikon feltöltése. Követően egy hero ikon, van azonban nem rendelkeznek való törléséhez a közzétételi portálon. Kövesse a [hero ikon irányelvek](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Nyissa meg a **közzététel** fülre, majd **átmeneti üzembe**. Tesztelés az ajánlat átmeneti környezetben részletes útmutatásért lásd: [tesztelése a Virtuálisgép-ajánlat a Marketplace-en](marketplace-publishing-vm-image-test-in-staging.md).
8. Az átmeneti állapotában is az ajánlat tesztelését, követően nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.

    ![Emblémát](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Frissítés a Termékváltozat leírása
A Termékváltozat adatok frissítése, és tegye közzé újra az ajánlatot, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).
2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **MARKETING** fülre.
4. Kattintson a **angol (US)**.
5. Kattintson a **csomagok** fülre. Az a **termékváltozatok** szakaszban, a Termékváltozat frissítése **cím**, **összefoglalás**, és **leírás** , és mentse a módosításokat.

   > [!NOTE]
   > Amikor frissíti a Termékváltozatának adatait, vegye figyelembe ezeket a korlátozásokat: 
   * Ismétlődő szöveg ne adja meg az ajánlat leírása és a Termékváltozat leírása. 
   * Ne adja meg a Termékváltozat-cím és az ajánlat hosszú összegzése ismétlődő szöveget. 
   * Ne adja meg a Termékváltozat-cím és az ajánlat összefoglalásának ismétlődő szöveget.
   >
   >
6. Nyissa meg a **közzététel** fülre, majd **átmeneti üzembe**. Tesztelés az ajánlat átmeneti környezetben részletes útmutatásért lásd: [tesztelése a Virtuálisgép-ajánlat a Marketplace-en](marketplace-publishing-vm-image-test-in-staging.md).
7. Az átmeneti állapotában is az ajánlat tesztelését, követően nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.

    ![Tervek](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Módosítsa a meglévő kapcsolatokat, vagy új hivatkozások hozzáadása
Meglévő kapcsolatokat, vagy új hivatkozások hozzáadása és majd közzé az ajánlatot még módosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).
2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **MARKETING** fülre.
4. Kattintson a **angol (US)**.
5. Kattintson a **hivatkozások** fülre.
6. Egy új kapcsolat hozzáadása a a **hivatkozások** területén kattintson **+ Hozzáadás hivatkozás**. Az a **hivatkozás hozzáadása** párbeszédpanelen adja meg a hivatkozás **cím** és **URL-cím** , és mentse a módosításokat. Minden olyan hivatkozás, amely segít az ügyfeleknek információkat tartalmazó adhat meg.
7. Frissítéséhez vagy törléséhez egy meglévő, válassza ki a hivatkozást, majd kattintson a **szerkesztése** gomb vagy a **törlése** gombra.

   > [!NOTE]
   > Győződjön meg arról, a hivatkozások, ebben a szakaszban megadott működik megfelelően, mert ezek a hivatkozások meggyőződik arról a gyártási folyamat során.
   >
   >
8. Nyissa meg a **közzététel** fülre, majd **átmeneti üzembe**. Tesztelés az ajánlat átmeneti környezetben részletes útmutatásért lásd: [tesztelése a Virtuálisgép-ajánlat a Marketplace-en](marketplace-publishing-vm-image-test-in-staging.md).
9. Az átmeneti állapotában is az ajánlat tesztelését, követően nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.

    ![Hivatkozások](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Hivatkozás hozzáadása](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Módosíthatja egy meglévő képet, vagy adjon hozzá egy új képet
Módosíthatja egy meglévő képet, vagy adja hozzá az új minta képeket, és majd közzé az ajánlatot még, kövesse az alábbi lépéseket:

> [!NOTE]
> Csak egy képet jelenik meg a [az Azure portal](https://portal.azure.com).
>
>

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).
2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **MARKETING** fülre.
4. Kattintson a **angol (US)**.
5. Kattintson a **MINTAKÉPEKET** fülre.
6. Egy új képet hozzáadása a a **mintaképeket** területén kattintson **új LEMEZKÉP FELTÖLTÉSÉHEZ** , és mentse a módosításokat.

   > [!NOTE]
   > Például egy képet nem kötelező.
   >
7. Nyissa meg a **közzététel** fülre, majd **átmeneti üzembe**. Tesztelés az ajánlat átmeneti környezetben részletes útmutatásért lásd: [tesztelése a Virtuálisgép-ajánlat a Marketplace-en](marketplace-publishing-vm-image-test-in-staging.md).
8. Az átmeneti állapotában is az ajánlat tesztelését, követően nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.

    ![Mintaképeket](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>A jogi tartalom frissítése
A jogi tartalmát frissíti, és közzé az ajánlatot még, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).
2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **MARKETING** fülre.
4. Kattintson a **angol (US)**.
5. Kattintson a **jogi** fülre. Az a **jogi** szakaszban, frissítse a szabályzatokat és használati feltételekre. Írja be vagy illessze be a szabályzatokra és használati a **használati** mezőbe, és mentse a módosításokat.
6. A használati jogi feltételek karakter határa 1 millió karakter.
7. Nyissa meg a **közzététel** fülre, majd **átmeneti üzembe**. Tesztelés az ajánlat átmeneti környezetben részletes útmutatásért lásd: [tesztelése a Virtuálisgép-ajánlat a Marketplace-en](marketplace-publishing-vm-image-test-in-staging.md).
8. Az átmeneti állapotában is az ajánlat tesztelését, követően nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.

    ![Jogi tudnivalók](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>Támogatási információk frissítése
Támogatási információk frissítéséhez, és tegye közzé újra az ajánlatot, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).
2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **támogatási** fülre.
4. Az a **mérnöki forduljon** szakaszban, a mérnöki kapcsolattartási adatainak frissítéséhez. Ezeket az adatokat csak a partner és a Microsoft belső kommunikációra szolgálnak.
5. Az a **ügyfélszolgálat** szakaszban, a támogatási kapcsolattartási adatainak frissítéséhez és a **TÁMOGATJA az URL-cím**. Ezeket az adatokat csak a partner és a Microsoft belső kommunikációra szolgálnak.

   > [!NOTE]
   > Ha azt szeretné, csak az e-mailes támogatást biztosít, adja meg a helyőrző telefonszámot a **ügyfélszolgálat** szakaszban. Ebben az esetben az e-mailt a megadott használja.
   >
   >
6. Nyissa meg a **közzététel** fülre, majd **átmeneti üzembe**. Tesztelés az ajánlat átmeneti környezetben részletes útmutatásért lásd: [tesztelése a Virtuálisgép-ajánlat a Marketplace-en](marketplace-publishing-vm-image-test-in-staging.md).
7. Az átmeneti állapotában is az ajánlat tesztelését, követően nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.

    ![Támogatás](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>A kategóriák frissítése
Az ajánlatban kategóriák szakaszban, és tegye közzé újra az ajánlatot, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).
2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **kategóriák** fülre.
4. Az a **kategóriák** részen frissíteni az ajánlatot a kategóriák és a módosítások mentéséhez. Kiválaszthatja, hogy az Azure piactér-katalógusra legfeljebb öt kategóriáit.
5. Nyissa meg a **közzététel** fülre, majd **átmeneti üzembe**. Tesztelés az ajánlat átmeneti környezetben részletes útmutatásért lásd: [tesztelése a Virtuálisgép-ajánlat a Marketplace-en](marketplace-publishing-vm-image-test-in-staging.md).
6. Az átmeneti állapotában is az ajánlat tesztelését, követően nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.

    ![Kategóriák](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Adjon hozzá egy új Termékváltozatot alatt felsorolt ajánlat
Adjon hozzá egy új Termékváltozatot az élő ajánlatban, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).
2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **Termékváltozatok** fülre. Kattintson a **adjon hozzá egy Termékváltozatot**. 
4. A párbeszédpanelen adja meg egy **Termékváltozat azonosítója** kisbetűs. Válassza ki a **használata a saját licencét (BYOL) számlázási modell** jelölőnégyzetet, ha szeretne közzétenni egy BYOL számlázási modellt az új Termékváltozat. Ellenkező esetben törölje a jelölőnégyzet jelölését. Kattintson az osztásjel hozhat létre egy új Termékváltozatban. Ha nem választja ki a BYOL számlázási modell, a számlázási modell értéke automatikusan óránként történik. Ha a 30 napos ingyenes próbaverzió óránkénti számlázási modell, jelölje be **egy hónap** a **érhető el egy ingyenes próbaverzióra?** Ellenkező esetben válassza **nem próbaverzió**. (**Érhető el egy ingyenes próbaverzióra?**  csak akkor, ha az új Termékváltozat létrehozása közben kiválasztott BYOL még nem jelenik meg.)

   > [!IMPORTANT]
   > **Ez a Termékváltozat a Marketplace-ről elrejtése, mert azt kell mindig vásárolható keresztül megoldássablon** kell **Igen** *csak* Ha megoldássablon közzétételéhez használt jóvá. Ellenkező esetben ez a beállítás mindig kell **nem**.
   >
   >
4. A bal oldali menüben kattintson a **Virtuálisgép-rendszerképek** lapra, és ismerje meg a létrehozott új Termékváltozat.
5. Az új Termékváltozat beállításával kapcsolatban lásd: [a Virtuálisgép-lemezkép minősítés beszerzése](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) útmutatást.
6. Adja hozzá az új termékváltozat marketinganyagot, lásd: [marketing-tartalmat meg piactér](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Az új termékváltozat díjszabási információk hozzáadása, lásd: [az árak megadása](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Nyissa meg a **közzététel** fülre, majd **átmeneti üzembe**. Tesztelés az ajánlat átmeneti környezetben részletes útmutatásért lásd: [tesztelése a Virtuálisgép-ajánlat a Marketplace-en](marketplace-publishing-vm-image-test-in-staging.md).
9. Az átmeneti állapotában is az ajánlat tesztelését, követően nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.

    ![Termékváltozatok](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Adjon hozzá egy Termékváltozatot](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>Az adatlemezek száma a listán szereplő termékváltozat módosítása
Nem növelési és csökkentési az adatlemezek száma a listán szereplő termékváltozat. Ebben az esetben egy új Termékváltozatban létrehozásához szükséges. Részletes útmutatásért tekintse meg a szakasz [adjon hozzá egy új Termékváltozatot a felsorolt ajánlatnak](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>A Marketplace-ről egy listán szereplő ajánlat törlése
Üzemeltetés minőségének különböző szempontjait kell lennie elvégzi, egy kérelem egy élő ajánlat eltávolítása esetén. Útmutatást kaphat a támogatási csapat, távolítsa el a felsorolt ajánlatot a piactérről, kövesse az alábbi lépéseket:

1. Létre egy támogatási jegyet a a [incidens létrehozása](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) lapot.

2. Válassza ki **Problématípus** , **ajánlatok kezelésével**, és válassza ki **kategória** , **módosítása egy ajánlat és/vagy a Termékváltozat már éles környezetben**.
3. Küldje el a kérelmet.

A támogatási csapat végigvezeti az ajánlat/SKU törlése folyamatban.

> [!NOTE]
> Az ajánlat bármikor törölheti, bár a Vázlat állapot (de nem előkészítési vagy termelési). Az a **előzmények** lapra, majd **ELVETI DRAFT**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>A listában szereplő SKU törlése a Marketplace-ről
A Marketplace-ről a listában szereplő SKU törléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).

2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali ablaktáblán kattintson a **Termékváltozatok** fülre.
4. Válassza ki a törölni, és kattintson a kívánt Termékváltozatát a **törlése** gombra.
5. Nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.
6. Miután az ajánlatot a piactéren ismételt közzététele, a Termékváltozat törlődik a piactérről, és az Azure Portalon.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>A jelenlegi verziója a listában szereplő SKU törlése a Marketplace-ről
A listában szereplő SKU jelenlegi verziója a Marketplace-ről törléséhez kövesse az alábbi lépéseket: 

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).

2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **Virtuálisgép-rendszerképek** fülre.
4. Válassza ki a Termékváltozat, amelynek aktuális verzióját szeretné törölni, majd kattintson a **törlése** gombra.
5. Nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.
6. Az ajánlat beolvasása újra közzé a piactéren, miután a listában szereplő SKU jelenlegi verziója törlődik a piactérről, és az Azure Portalon. A Termékváltozat majd vissza lesz állítva a korábbi verzióra.

## <a name="revert-the-listing-price-to-production-values"></a>Visszaállítás a termelési értékekkel a listaelem ár
Vissza a tőzsdei árát termelési értékekkel, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).
2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **DÍJSZABÁSI** fülre.
4. Válassza ki a régiót, amelynek díjszabása vissza szeretné állítani.

    ![A díjszabás régió](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. SKU-k egy óránkénti számlázási modell alaphelyzetbe az árak a Processzormagok száma, ha azok éles környezetben a kiválasztott régióban. Az SKU-k egy BYOL számlázási modell, elérhetővé teszi a Termékváltozat a régióban a jelölőnégyzet bejelölésével a termékváltozat a **EXTERNALLY-LICENSED (BYOL) Termékváltozat rendelkezésre ÁLLÁSI** szakaszban.

    ![Díjszabási modellel](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Kattintson a **AUTOPRICE más piacok alapján díjai az Egyesült Államok**.

   > [!NOTE]
   > A gomb címkéje a kiválasztott régiójától függően eltérő lehet. Egyesült Államok választottunk, mert a gomb címkéje **AUTOPRICE egyéb piacok alapú ON árak az Egyesült Államok**.
   >
   >

    ![Autoprice](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. A lapon a Autoprice varázsló 1 Válassza ki az alapszintű piacon, és kattintson a **nyíl** gombra.

    ![Alap piaci](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. A 2 lapon, válassza ki a szolgáltatási csomagokat és a mérőszámok (magok), majd kattintson a **nyíl** gombra.

    ![Szolgáltatási csomagok és mérőszámok](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. 3. oldalon, kattintson **váltógomb összes** minden régió kiválasztásához. Vagy ha manuálisan végrehajtja az adott régióban jelölőnégyzetét. Kattintson a **nyíl** gombra.

    ![Válassza ki a piacot](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. A lapon 4, tekintse át az átváltási árfolyamok, és kattintson a **Befejezés**. A varázsló alaphelyzetbe állítja a díjszabás megfelelően a beállításokat.

11. Az a **DÍJSZABÁSI** lapra, majd **összegzése és a változások megtekintése**.
    A **verzió megtekintése**, jelölje be **Draft**, és a **összehasonlítandó**válassza **éles**. Ha Ön nem díjszabási különbséget látni, a díjszabás vissza termelési értékekkel sikeres.

    ![Összefoglalás megtekintése és a változások](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Nyissa meg a **közzététel** fülre, majd **átmeneti üzembe**. Tesztelés az ajánlat átmeneti környezetben részletes útmutatásért lásd: [tesztelése a Virtuálisgép-ajánlat a Marketplace-en](marketplace-publishing-vm-image-test-in-staging.md).
13. Az átmeneti állapotában is az ajánlat tesztelését, követően nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.

## <a name="revert-the-billing-model-to-production-values"></a>Visszaállítás a termelési értékekkel a számlázási modell
A számlázási modellt éles értékek visszaállításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).

2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **Termékváltozatok** fülre.
4. Kattintson a **szerkesztése** gombra a a számlázási modellt. A megnyíló ablakban jelölje be vagy törölje a **számlázási és licencelési történik együtt a külsőleg (más néven a saját licenc használatát) Azure-ból** jelölőnégyzetet.

    ![A számlázás szerkesztése](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Kövesse a "Revert termelési értékekkel a listaelem ár" Ebben a cikkben.
6. Nyissa meg a **közzététel** fülre, majd **átmeneti üzembe**. Tesztelés az ajánlat átmeneti környezetben részletes útmutatásért lásd: [tesztelése a Virtuálisgép-ajánlat a Marketplace-en](marketplace-publishing-vm-image-test-in-staging.md).
7. Az átmeneti állapotában is az ajánlat tesztelését, követően nyissa meg a **közzététel** lapján a közzétételi portálon. Kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Az éles értéket egy listán szereplő SKU láthatóságának beállítását visszaállítani
Az éles értéket egy listán szereplő SKU láthatóságának beállítását visszaállításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).

2. Nyissa meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **Termékváltozatok** fülre.
4. Válassza ki a Termékváltozat, és visszaállítja a láthatósági a termékváltozat a termelési értékre.

    ![Láthatóság](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Miután végzett a módosításokkal, kattintson a **kérelem jóváhagyása a LEKÜLDÉSES, ÉLES** való ismételt közzétételéhez az ajánlatot a piactéren.

## <a name="see-also"></a>Lásd még
* [Első lépések: Az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)
* [Küldenie jóváhagyásra cége kifizetési reporting ismertetése](marketplace-publishing-report-payout.md)
* [A Cloud Solution Provider viszonteladói élhetnek módosítása](marketplace-publishing-csp-incentive.md)
* [A Marketplace-en közzétételi gyakori problémáinak elhárítása](marketplace-publishing-support-common-issues.md)
* [Támogatás kérése a közzétevő](marketplace-publishing-get-publisher-support.md)
* [A helyszíni Virtuálisgép-lemezkép létrehozása](marketplace-publishing-vm-image-creation-on-premise.md)
* [Az Azure betekintő portálon Windows rendszerű virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
