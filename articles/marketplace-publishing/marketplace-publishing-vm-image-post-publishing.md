---
title: A virtuálisgép-lemezkép az Azure piactéren kezelése |} Microsoft Docs
description: Részletes útmutató a virtuálisgép-lemezkép az Azure piactéren kezdeti közzétételét követően kezelésének módját
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: mbaldwin;
ms.openlocfilehash: 19f60de4a6c651885da7ff1fb7612a77f3d0c8f1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
ms.locfileid: "29949021"
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>A virtuális gép ajánlatok az Azure piactéren utáni éles útmutató
Ez a cikk azt ismerteti, hogyan frissítheti az olyan élő virtuális gép ajánlat az Azure piactéren. Végigvezeti egy vagy több új termékváltozatok hozzáadása egy meglévő ajánlatra. Azt is végigvezeti Önt egy élő virtuális gép ajánlat vagy SKU eltávolítása a piactér folyamata.

Miután egy ajánlat/SKU elő van készítve az a [Azure-portálon](http://portal.azure.com), nem módosítható a következő szövegmezőbe:

* **Ajánlat azonosítója**: az a közzététel-portálon lépjen **virtuális gépek** válassza ki az ajánlatot. Kattintson a **Virtuálisgép-rendszerképek** > **kínálnak azonosító**.
* **Termékváltozat-azonosító**: az a közzététel-portálon lépjen **virtuális gépek** válassza ki az ajánlatot. Kattintson a **Termékváltozatok** > **adja hozzá a Termékváltozat**.
* **A Publisher Namespace**: az a közzététel-portálon lépjen **virtuális gépek** > **forgatókönyv** > **mondja el nekünk kapcsolatban a vállalati**(az található a "Lépés 2 regisztrálása a vállalati") > **Publisher Namespace** > **Namespace**.

Az ajánlat/SKU szerepel-e után a [piactér](http://azure.microsoft.com/marketplace), nem módosíthatja az alábbi mezőkbe:

* **Ajánlat azonosítója**: az a közzététel-portálon lépjen **virtuális gépek** válassza ki az ajánlatot. Kattintson a **Virtuálisgép-rendszerképek** > **kínálnak azonosító**.
* **Termékváltozat-azonosító**: az a közzététel-portálon lépjen **virtuális gépek** válassza ki az ajánlatot. Kattintson a **Termékváltozatok** > **adja hozzá a Termékváltozat**.
* **A Publisher Namespace**: az a közzététel-portálon lépjen **virtuális gépek** > **forgatókönyv** > **mondja el nekünk kapcsolatban a vállalati**("Lépés 2 regisztrálása" alatt található) **Publisher Namespace** > **Namespace**.
* **Portok**: az a közzététel-portálon lépjen **virtuális gépek** válassza ki az ajánlatot. Kattintson a **Virtuálisgép-rendszerképek** > **megnyitott portok**.
* **Módosítsa a felsorolt SKU(s) díjszabása**
* **Felsorolt SKU(s) számlázási modell megváltozott**
* **A felsorolt SKU(s) régiói számlázási törlése**
* **A felsorolt SKU(s) adatlemez módosítása**

## <a name="update-the-technical-details-of-a-sku"></a>A metódust a műszaki részleteinek frissítése
Új verzió a listában szereplő SKU és ismételt közzététele az ajánlatot, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).
2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **Virtuálisgép-rendszerképek** fülre.
4. Az a **termékváltozatok** területén keresse meg a frissíteni kívánt Termékváltozat.
5. Adja hozzá egy új verziószámot a termékváltozat, majd kattintson a **+** gombra. Az új verzió X.Y.Z formátumban, ahol X, Y és Z egész számnak kell lennie. Megváltozik a verzió csak növekményes kell lennie.
6. Az a **az operációs rendszer virtuális merevlemez URL-cím** mezőbe, írja be a virtuális merevlemez operációs rendszerhez létrehozott URI közös hozzáférésű jogosultságkódot, és menti a módosításokat.

   > [!IMPORTANT]
   > Ön nem növekvő/csökkentést adatlemez felsorolt metódust. Ebben az esetben egy új SKU létrehozásához szükséges. Részletes útmutatásért tekintse át a részt [hozzáadása egy új SKU felsorolt ajánlat keretében](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Lépjen a **közzététel** fülre, majd **LEKÜLDÉSES átmeneti**. Az ajánlat tesztelése az átmeneti környezetben a részletes útmutatóért lásd: [tesztelése a virtuális gép-ajánlat a piactér](marketplace-publishing-vm-image-test-in-staging.md).
8. Miután tesztelte az ajánlatot a tesztelési, lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.

    ![Virtuálisgép-rendszerképek](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Az ajánlat vagy egy SKU felhasználóknak részleteinek frissítése
Frissítheti a felhasználóknak (marketing, jogi, támogatják, kategóriák) a élő ajánlat vagy a piactéren SKU részleteit.

### <a name="update-the-offer-description-and-logos"></a>Az ajánlat leírása és emblémák frissítése
Az ajánlat részleteihez és ismételt közzététele az ajánlatot frissítéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).
2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **MARKETING** fülre.
4. Kattintson a **angol (amerikai nyelv)**.
5. Kattintson a **részletek** fülre. Az a **leírás** szakaszban, frissítse az ajánlat **cím**, **összegzés**, és **hosszú összefoglalás** , és mentse a módosításokat.

   > [!NOTE]
   > Ha frissíti a Termékváltozat részletes adatait, a korlátozások figyelembe: 
   * Ismétlődő szöveg ne adja meg az ajánlat leírása és a Termékváltozat leírását.
   * Ne adja meg a Termékváltozat-cím és a hosszú összefoglalás ajánlat ismétlődő szöveg. 
   * Ne adja meg az SKU-cím és az ajánlat-összefoglaló ismétlődő szöveg.
   >
   >

    ![Részletek](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. Az a **emblémák** szakasza a **részletek** lapra, frissítse az emblémát. Győződjön meg arról, hogy az egyes emblémák kövesse a [Azure piactér irányelvek](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > A kiemelt ikon használata nem kötelező. Választhat egy kiemelt ikon feltöltéséhez nem. Miután egy kiemelt ikon töltheti fel, van azonban nem rendelkezik való törléséhez a közzétételi portálon. Kövesse a [hero ikon irányelvek](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Lépjen a **közzététel** fülre, majd **LEKÜLDÉSES átmeneti**. Az ajánlat tesztelése az átmeneti környezetben a részletes útmutatóért lásd: [tesztelése a virtuális gép-ajánlat a piactér](marketplace-publishing-vm-image-test-in-staging.md).
8. Miután tesztelte az ajánlatot a tesztelési, lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.

    ![Emblémák](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Frissítés a Termékváltozat leírása
A Termékváltozat adatainak frissítéséhez és ismételt közzététele az ajánlatot, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).
2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **MARKETING** fülre.
4. Kattintson a **angol (amerikai nyelv)**.
5. Kattintson a **tervek** fülre. Az a **termékváltozatok** szakaszban, frissítse a Termékváltozat **cím**, **összegzés**, és **leírás** , és mentse a módosításokat.

   > [!NOTE]
   > Ha frissíti a Termékváltozat részletes adatait, a korlátozások figyelembe: 
   * Ismétlődő szöveg ne adja meg az ajánlat leírása és a Termékváltozat leírását. 
   * Ne adja meg a Termékváltozat-cím és a hosszú összefoglalás ajánlat ismétlődő szöveg. 
   * Ne adja meg az SKU-cím és az ajánlat-összefoglaló ismétlődő szöveg.
   >
   >
6. Lépjen a **közzététel** fülre, majd **LEKÜLDÉSES átmeneti**. Az ajánlat tesztelése az átmeneti környezetben a részletes útmutatóért lásd: [tesztelése a virtuális gép-ajánlat a piactér](marketplace-publishing-vm-image-test-in-staging.md).
7. Miután tesztelte az ajánlatot a tesztelési, lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.

    ![Tervek](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Módosítsa a meglévő kapcsolatokat, vagy adja hozzá az új hivatkozások
Módosítsa a meglévő kapcsolatokat, vagy új hivatkozások hozzáadása, és újbóli az ajánlatot, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).
2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **MARKETING** fülre.
4. Kattintson a **angol (amerikai nyelv)**.
5. Kattintson a **hivatkozások** fülre.
6. Egy új hivatkozás hozzáadása a a **hivatkozások** kattintson **+ Hozzáadás HIVATKOZÁSRA**. Az a **hivatkozás hozzáadása** párbeszédpanelen adja meg a hivatkozás **cím** és **URL-cím** , és mentse a módosításokat. Megadhatja, hogy minden hivatkozás, amely segít a felhasználóknak információkat tartalmaz.
7. Frissítés, vagy törölje a meglévő hivatkozás, jelölje ki a kapcsolatot, majd kattintson a **szerkesztése** gombra vagy a **törlése** gombra.

   > [!NOTE]
   > Győződjön meg arról, a hivatkozások, ebben a szakaszban megadott működik megfelelően, mert ezek a hivatkozások beolvasása érvényesíti kérelem folyamat során.
   >
   >
8. Lépjen a **közzététel** fülre, majd **LEKÜLDÉSES átmeneti**. Az ajánlat tesztelése az átmeneti környezetben a részletes útmutatóért lásd: [tesztelése a virtuális gép-ajánlat a piactér](marketplace-publishing-vm-image-test-in-staging.md).
9. Miután tesztelte az ajánlatot a tesztelési, lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.

    ![Hivatkozások](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Hivatkozás hozzáadása](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Módosítsa a meglévő mintát lemezképet, vagy adjon hozzá egy új minta lemezképet
Egy meglévő minta kép vagy új minta lemezképek hozzáadása és újbóli ajánlatát módosításához kövesse az alábbi lépéseket:

> [!NOTE]
> Csak egy minta kép megjelenik a [Azure-portálon](https://portal.azure.com).
>
>

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).
2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **MARKETING** fülre.
4. Kattintson a **angol (amerikai nyelv)**.
5. Kattintson a **minta képek** fülre.
6. Új minta-lemezkép hozzáadása a a **minta képek** kattintson **új LEMEZKÉP FELTÖLTÉSE** , és mentse a módosításokat.

   > [!NOTE]
   > Többek között a minta-lemezkép nem kötelező megadni.
   >
7. Lépjen a **közzététel** fülre, majd **LEKÜLDÉSES átmeneti**. Az ajánlat tesztelése az átmeneti környezetben a részletes útmutatóért lásd: [tesztelése a virtuális gép-ajánlat a piactér](marketplace-publishing-vm-image-test-in-staging.md).
8. Miután tesztelte az ajánlatot a tesztelési, lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.

    ![A minta lemezképek](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>A jogi tartalom frissítése
A jogi tartalmát frissíti, és az ajánlatot közzétenni, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).
2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **MARKETING** fülre.
4. Kattintson a **angol (amerikai nyelv)**.
5. Kattintson a **jogi** fülre. Az a **jogi** szakaszban, a házirendek/használati feltételek frissítése. Írja vagy illessze be a házirendek/feltételeit a **használati** mezőben, és mentse a módosításokat.
6. A jogi használati feltételeket karakter határa 1 millió karaktereket.
7. Lépjen a **közzététel** fülre, majd **LEKÜLDÉSES átmeneti**. Az ajánlat tesztelése az átmeneti környezetben a részletes útmutatóért lásd: [tesztelése a virtuális gép-ajánlat a piactér](marketplace-publishing-vm-image-test-in-staging.md).
8. Miután tesztelte az ajánlatot a tesztelési, lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.

    ![Jogi tudnivalók](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>A támogatási információk frissítése
A támogatási információk frissítéséhez, majd ismételt közzététele az ajánlatot, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).
2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **támogatási** fülre.
4. Az a **forduljon a mérnöki** területen a mérnöki kapcsolattartási adatainak frissítéséhez. Ezek az adatok csak a partnerek és a Microsoft között belső kommunikációs érvényesek.
5. Az a **ügyfélszolgálatot** szakaszban, a támogatási kapcsolattartási adatainak frissítéséhez és a **TÁMOGATJA az URL-cím**. Ezek az adatok csak a partnerek és a Microsoft között belső kommunikációs érvényesek.

   > [!NOTE]
   > Ha lehetővé szeretné tenni a csak az e-mailes támogatást, adjon meg egy üres telefonszámot a **ügyfélszolgálatot** szakasz. Ebben az esetben az e-mailben megadott használja.
   >
   >
6. Lépjen a **közzététel** fülre, majd **LEKÜLDÉSES átmeneti**. Az ajánlat tesztelése az átmeneti környezetben a részletes útmutatóért lásd: [tesztelése a virtuális gép-ajánlat a piactér](marketplace-publishing-vm-image-test-in-staging.md).
7. Miután tesztelte az ajánlatot a tesztelési, lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.

    ![Támogatás](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>A kategóriák frissítése
Az ajánlat kategóriák szakaszban, és az ajánlatot közzétenni, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).
2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **kategóriák** fülre.
4. Az a **kategóriák** szakaszt, frissítse a kategóriák az előfizetéshez, és menti a módosításokat. Lehetősége van az Azure piactér galéria legfeljebb öt kategóriába.
5. Lépjen a **közzététel** fülre, majd **LEKÜLDÉSES átmeneti**. Az ajánlat tesztelése az átmeneti környezetben a részletes útmutatóért lásd: [tesztelése a virtuális gép-ajánlat a piactér](marketplace-publishing-vm-image-test-in-staging.md).
6. Miután tesztelte az ajánlatot a tesztelési, lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.

    ![Kategóriák](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Adja hozzá a felsorolt ajánlat keretében egy új Termékváltozat
Az élő ajánlatot egy új SKU hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).
2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **Termékváltozatok** fülre. Kattintson a **adja hozzá a Termékváltozat**. 
4. A párbeszédpanelen adja meg a **SKU-azonosítója** kisbetűs. Válassza ki a **kapcsolja a saját licenc (használata BYOL) számlázási modellt** jelölőnégyzetet, ha az új SKU BYOL számlázási modellt közzé szeretné tenni. Minden más esetben törölje a jelölőnégyzet jelölését. Kattintson az osztásjel egy új SKU létrehozásához. Ha nem a BYOL számlázási modellt, a számlázási modellt értéke automatikusan óránként. Ha a 30 napos ingyenes próbaverzióval óránkénti számlázási modell, jelölje be **egy hónap** a **érhető el egy ingyenes próbaverzióra?** Máskülönben válassza **nem próbaverzió**. (**Érhető el egy ingyenes próbaverzióra?**  jelenik meg, csak akkor, ha nincs kiválasztva BYOL az új SKU létrehozása során.)

   > [!IMPORTANT]
   > **A Termékváltozat a piactérről elrejtése, mert azt kell mindig vásárolható keresztül a megoldássablon** kell **Igen** *csak* Ha közzétételéhez a megoldássablon még jóvá. Ellenkező esetben ez a beállítás mindig kell **nem**.
   >
   >
4. A bal oldali menüben kattintson a **Virtuálisgép-rendszerképek** lapra, és kideríti, hogy a létrehozott új Termékváltozat.
5. Állítsa be az új SKU, lásd: [kifutott a VM-lemezkép](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) útmutatót.
6. Adja hozzá az új termékváltozat marketinganyagokat, lásd: [tartalom marketing meg piactér](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Adja hozzá az új termékváltozat díjszabási információkat, lásd: [az árak](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Lépjen a **közzététel** fülre, majd **LEKÜLDÉSES átmeneti**. Az ajánlat tesztelése az átmeneti környezetben a részletes útmutatóért lásd: [tesztelése a virtuális gép-ajánlat a piactér](marketplace-publishing-vm-image-test-in-staging.md).
9. Miután tesztelte az ajánlatot a tesztelési, lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.

    ![Termékváltozatok](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Adja hozzá a Termékváltozat](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>A felsorolt termékváltozat adatlemez módosítása
Ön nem növekvő/csökkentést adatlemez felsorolt metódust. Ebben az esetben egy új SKU létrehozásához szükséges. Részletes útmutatásért tekintse át a részt [hozzáadása egy új SKU felsorolt ajánlat keretében](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>A felsorolt ajánlat törlése a piactérről
Különböző szempontjairól kell kell venni beállításokat egy olyan élő ajánlat eltávolítására vonatkozó kérelem esetén. Ahhoz, hogy útmutatást a támogatási csapat felsorolt ajánlat eltávolítása a piactérről, kövesse az alábbi lépéseket:

1. A támogatási jegy emelje a [incidens létrehozása](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) lap.

2. Válassza ki **problématípust** , **ajánlatok kezelése**, és válassza ki **kategória** , **módosítása egy ajánlatot és/vagy a Termékváltozat már éles környezetben**.
3. A kérelmet.

A támogatási csoport végigvezeti Önt az ajánlat/SKU törlési folyamat.

> [!NOTE]
> Az ajánlat mindig törölheti, amíg vázlat állapotba (de nem átmeneti vagy üzemi) módban. Az a **előzmények** lapra, majd **ELVETÉSE VÁZLAT**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>A felsorolt SKU törlése a piactérről
A felsorolt SKU törli a piactérről, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).

2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali ablaktáblában kattintson a **Termékváltozatok** fülre.
4. Válassza ki a használni kívánt törölje, majd kattintson az SKU a **törlése** gombra.
5. Lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.
6. Után az ajánlatot a piactéren ismételt közzététele, az SKU törlődik a piactéren, és az Azure-portálon.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>A felsorolt SKU jelenlegi verziója törlése a piactérről
Törli a felsorolt SKU jelenlegi verziója a piactérről, kövesse az alábbi lépéseket: 

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).

2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **Virtuálisgép-rendszerképek** fülre.
4. Válassza ki a SKU, amelynek aktuális verzióját szeretné törölje, majd kattintson a **törlése** gombra.
5. Lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.
6. Után az ajánlat lekérdezi újból közzé a piactéren, a felsorolt SKU jelenlegi verziója törlődik a piactéren, és az Azure-portálon. A Termékváltozat majd vissza lesz állítva az előző verziójára.

## <a name="revert-the-listing-price-to-production-values"></a>Állítsa vissza az éles értékek listaelem ár
Éles értékek listaelem ár visszaállításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).
2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **ÁRAZÁS** fülre.
4. Válasszon ki egy régiót, amelynek árképzési alaphelyzetbe állítja.

    ![Árképzési régiók](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. SKU óránkénti számlázási modellel, az vissza az összes mag árakat, a kijelölt terület éles vannak. Az SKU BYOL számlázási modell, elérhetővé a Termékváltozat régióban a termékváltozat a jelölőnégyzet bejelölésével a **EXTERNALLY-LICENSED (BYOL) SKU rendelkezésre ÁLLÁSI** szakasz.

    ![Árképzési modellekkel](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Kattintson a **PIACOKRA AUTOPRICE az Egyesült Államokban árak alapján**.

   > [!NOTE]
   > A gomb címkéje a kiválasztott régió függően eltérőek lehetnek. Az Amerikai Egyesült Államok választott azt, mert a gomb címkéje **AUTOPRICE egyéb piacok alapú ON árak része az Egyesült Államok**.
   >
   >

    ![Autoprice](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. A lapon a Autoprice varázsló 1 Válassza ki az alap piacra, és kattintson a **nyíl** gombra.

    ![Alap piaci](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. A 2 lapon, válassza ki a service-csomagokról és mérőszámok (magok), majd kattintson a **nyíl** gombra.

    ![Service-csomagokról és mérőszámok](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. 3 lapján kattintson a **váltása összes** minden egyes kiválasztásához. Vagy manuálisan kiválaszthatja az adott régióban jelölőnégyzeteket. Kattintson a **nyíl** gombra.

    ![Válassza ki a piacon](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. A lapon 4 ellenőrizze árfolyam, és kattintson a **Befejezés**. A varázsló alaphelyzetbe állítja az árképzés megfelelően a beállításokat.

11. Az a **ÁRAZÁS** lapra, majd **összegzése és módosítások**.
    A **verzió megtekintése**, jelölje be **vázlat**, és a **összehasonlításra**, jelölje be **éles**. Ha nincs árképzési különbség, a díjszabás tért vissza a termelési értékek sikeresen.

    ![Összefoglalás megtekintése és a változások](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Lépjen a **közzététel** fülre, majd **LEKÜLDÉSES átmeneti**. Az ajánlat tesztelése az átmeneti környezetben a részletes útmutatóért lásd: [tesztelése a virtuális gép-ajánlat a piactér](marketplace-publishing-vm-image-test-in-staging.md).
13. Miután tesztelte az ajánlatot a tesztelési, lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.

## <a name="revert-the-billing-model-to-production-values"></a>A számlázási modellt éles értékek visszaállításához
A számlázási modellt éles értékek visszaállításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).

2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **Termékváltozatok** fülre.
4. Kattintson a **szerkesztése** gombra a számlázási modelljét. A megjelenő ablakban válassza ki, vagy törölje a **számlázási és licencelési történik külsőleg (más néven a saját licenc) az Azure-ból** jelölőnégyzetet.

    ![Számlázási szerkesztése](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Kövesse a "Revert éles értékek listaelem ár" Ebben a cikkben.
6. Lépjen a **közzététel** fülre, majd **LEKÜLDÉSES átmeneti**. Az ajánlat tesztelése az átmeneti környezetben a részletes útmutatóért lásd: [tesztelése a virtuális gép-ajánlat a piactér](marketplace-publishing-vm-image-test-in-staging.md).
7. Miután tesztelte az ajánlatot a tesztelési, lépjen a **közzététel** a közzétételi lapján portálon. Kattintson a **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Állítsa vissza a láthatósági a felsorolt metódust az éles értékre
A felsorolt metódust az éles értékre a láthatósági visszaállításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).

2. Lépjen a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **Termékváltozatok** fülre.
4. Válassza ki a Termékváltozat, és állítsa vissza a láthatósági az éles értékre metódust.

    ![Láthatóság](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Miután a változtatásokat végzett, kattintson **kérelem JÓVÁHAGYÁSRA való LEKÜLDÉSES az ÉLES** ismételt közzététele az ajánlatot a piactéren.

## <a name="see-also"></a>Lásd még
* [Első lépések: Az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)
* [Kifizetés reporting ismertetése](marketplace-publishing-report-payout.md)
* [A Cloud Solution Provider viszonteladóhoz célzó olyan ösztönzők előnyeivel módosítása](marketplace-publishing-csp-incentive.md)
* [A piactéren közös közzétételi kapcsolatos problémák elhárítása](marketplace-publishing-support-common-issues.md)
* [Segítségre van szüksége közzétevőként](marketplace-publishing-get-publisher-support.md)
* [Hozzon létre egy Virtuálisgép-lemezkép helyszíni](marketplace-publishing-vm-image-creation-on-premise.md)
* [Az Azure betekintő portálon Windows rendszerű virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
