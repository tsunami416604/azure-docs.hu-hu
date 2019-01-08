---
title: Készítse elő és tesztelje ajánlatát az Azure piactéren való üzembe helyezéshez |} A Microsoft Docs
description: Részletes útmutatást nyújtó tartalom marketing, díjcsomagok konfigurálása és tesztelése az ajánlat az Azure piactéren való üzembe helyezés előtt.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: f1e6e779731564f714cc100d25a53da8732dca74
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075991"
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Végezze el a marketinganyagot az ajánlat létrehozása
Ebben a lépésben a közzétételi folyamat kell bizonyos marketinganyagot, és az ajánlat és/vagy az Azure piactéren elérhető termékváltozatok részleteit. Ha például fog adjon meg egy leírást a termék, vállalati emblémát, ár tervek, tervek részleteit és más információkat, amelyek szükségesek az ajánlat és/vagy a Termékváltozat leküldése átmeneti. Ez az információ lesz marketinganyagot az Azure Portalon. Megkezdődik a folyamatot a [közzétételi portál][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>1. lépés: Adja meg a Marketplace-en marketing-tartalom
*Angol az alapértelmezett és egyetlen támogatott nyelv.*  Ellenőrizze, hogy a mezők minden adatot angol nyelven. Az átmeneti üzem megkezdéséig az adatokat bármikor módosíthatja.

1. Nyissa meg a közzétételi portál [ https://publish.windowsazure.com ](https://publish.windowsazure.com).
2. A bal oldali menüben kattintson a **Marketing** fülre.
3. A fő panelen kattintson a **angol (amerikai)** gombra.
   
   > [!IMPORTANT]
   > Minden mező bejegyzéseket, köztük a képeket, hogy az átmeneti üzem tudni kell rendelkeznie.
   > 
   > 

### <a name="details-and-plans"></a>Részletek és csomagok
1. Adja meg az ajánlat címe (legfeljebb 50 karakter hosszú lehet), összegzése (legfeljebb 100 karakter) ajánlat, ajánlat (legfeljebb 256 karakter) hosszú összegzése, ajánlat leírása (legfeljebb 1300 karakter), emblémák alatt a **részletek** lap
2. Adja meg a terv title (legfeljebb 50 karakter hosszú lehet), terv összegzése (legfeljebb 100 karakter), a csomag leírása (legfeljebb 2000 karakter) a **csomagok** fülre.
   
   > [!NOTE]
   > Használhatja az alábbi HTML-címkéket az összesítést, mennyi ideig összefoglaló formázásához és az ajánlat és a csomag leírása. Az engedélyezett HTML-címkéket a következők h1, h2, h3, h4, h5, p, ol, ul, li, a [cél |} href], erős, em, a b i.
   > 
   > 
3. Ne adja meg az ajánlat és a terv leírását ismétlődő szöveg.
4. Ne adja meg a csomag címét és az ajánlat hosszú összegzése ismétlődő szöveget.
5. Ne adja meg a csomag címét ismétlődő szöveget és ajánlat összegzése.
6. Azonos terv címek ne adja meg egy ajánlat több energiaséma vonatkozik.
7. Tölthet fel képeket a kötelező specifikációnak (a közzétételi portálon szerepel), PNG formátumú, egy méreteire vonatkoztatva.
8. Győződjön meg arról, hogy az emblémát kövesse az alábbiakban leírt Azure Marketplace-en emblémáinak.
   
   ![rajz](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Az Azure Marketplace-en emblémáinak**

A közzétételi portálon feltöltött összes emblémák kövesse az alábbi irányelveket:

* Az Azure arculata egyszerű színpalettát használ. Tartsa számát az elsődleges és másodlagos színt az embléma alacsony.
* Az Azure Portal a témák színei fehér, és fekete. Ezért kerülje ezeket a színeket, a emblémák hátterének színét. Néhány színekkel, amely biztosítja, a emblémák neves az Azure Portalon. Javasoljuk az egyszerű alapszínek használatát. **Ha áttetsző hátterű használ, akkor ügyeljen arra, hogy a emblémák szöveg nem lesznek fehér vagy fekete vagy kék.**
* Ne használjon egy színátmenetes hátterének az embléma.
* Szöveg-, akár a vállalat vagy a márkanév, elkerüli az embléma. Az embléma megjelenését és működését "egyszerű" kell lennie, és kerülje átmenetekhez.
* Az embléma nem kell nyújtani.
* Kisebb emblémát 40 X 40 méretűnek kell lenniük képpont
* Közepes méretű embléma 90 X 90 méretűnek kell lenniük képpont
* Nagy méretű embléma 115 X 115 méretűnek kell lenniük képpont
* Széles körű embléma 255 X 115 méretűnek kell lenniük képpont
* Hero embléma 815 X 290 méretűnek kell lenniük képpont

> [!NOTE]
> A Hero embléma megadása nem kötelező. A közzétevő nem szeretné Hero emblémát tölthet fel. Azonban Miután feltöltött a hero ikon nem lehet törölni a közzétételi portálon. Ugyanakkor a partner Hero ikonok esetén az Azure Marketplace-en irányelveket kell követnie.
> 
> 

  ![rajz](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**További útmutatást a Hero embléma ikon (nem kötelező)**

* A Hero embléma megadása nem kötelező. A közzétevő nem szeretné Hero emblémát tölthet fel. **Azonban Miután feltöltött a hero ikon nem lehet törölni a közzétételi portálon. Ugyanakkor a partner kell kövesse a Azure Marketplace-en Hero ikonok másnak az ajánlat nem hagyható jóvá az éles környezetbe.**
* A közzétevő megjelenített neve, csomag cím és az ajánlat hosszú összegzése fehér a betűk színe megjelenik. Ezért kerülje a Hero ikon a háttérben gondoskodik a bármely világos szín. Fekete, a fehér és a transzparens háttér nem engedélyezett a Hero-ikonokat.
* A közzétevő megjelenített neve, csomag cím, az ajánlat hosszú összegzése és a Létrehozás gombra vannak beágyazva programozott módon a Hero embléma után kerül az ajánlat felsorolt. Így nem szöveg kell adnia, a Hero embléma tervezésekor. Ne változtassa meg üres területet a jobb oldalon, mert a szöveget (azt jelenti, közzétevő megjelenítési név, terv cím, az ajánlat hosszú összegzése) szerepelni fognak programozott módon, van ott. Az üres helyet a szövegnek kell lennie a 415 x 100, a jobb oldalon (és 370 ellensúlyozza, a balról képpont).
  
  ![rajz](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Hivatkozások
Az a **hivatkozások** lapra a bal oldali sávon, adja meg azokat az információkat, amelyek az ügyfelek segítségére lehetnek hivatkozásokat. Minden hivatkozás esetében adjon meg egy nevet és egy URL-címet.

![rajz](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>Mintaképeket (nem kötelező)
> [!NOTE]
> Például egy képet (nem kötelező).
> Annak ellenére, hogy feltöltheti a minta képeket törölhessen a közzétételi portál, csak egy rendszerképet (a rendszer véletlenszerűen választja ki) megjelenik az Azure Portalon. Ebből kifolyólag javasoljuk, hogy legfeljebb egy képet feltölteni.
> 
> 

Az a **mintaképeket** lapra a bal oldali menüben, és töltse fel az új lemezkép kattintva **töltsön fel egy új képet**. Ha rendelkezik egy meglévő lemezképet, és szeretné, cserélje le a, kattintson a **cserélje le a rendszerképet**.

![rajz](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Jogi tudnivalók
Az a **jogi** lapra, adja meg a házirendek és használati feltételekre mutató hivatkozást. Írja be vagy illessze be a feltételeket a nagyméretű **használati** mezőbe. A használati jogi feltételek karakter határa 1 000 000 karakternél.

![rajz](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Megjegyzés:** Virtuálisgép-ajánlat után egy ajánlat/SKU elő van készítve az Azure Portalon nem módosítható az alábbi mezőket:

* **Ajánlat azonosítója:** [közzétételi portál -> virtuális gépek -> az ajánlat Virtuálisgép-rendszerképek -> lapon-ajánlat azonosítója >]
* **Termékváltozat-azonosító:** [közzétételi portál -> virtuális gépek -> Válassza ki az ajánlat -> lap -> termékváltozatok adjon hozzá egy Termékváltozatot]
* **Közzétevő Namespace:** [közzétételi portál Virtual Machines -> lapon-Tell Us About (található alatt "Lépés 2 regisztrálni a vállalat") a vállalat -> > az útmutató -> közzétevő Namespace-Namespace >]

Virtuálisgép-ajánlat után az ajánlat/SKU szerepel az Azure Marketplace-en, nem módosítható az alábbi mezőket:

* **Ajánlat azonosítója:** [közzétételi portál -> virtuális gépek -> Válassza ki az ajánlat -> Virtuálisgép-rendszerképek-ajánlat azonosítója >]
* **Termékváltozat-azonosító:** [közzétételi portál -> virtuális gépek -> Válassza ki az ajánlat -> lap -> termékváltozatok adjon hozzá egy Termékváltozatot]
* **Közzétevő Namespace:** [közzétételi portál Virtual Machines -> lap -> útmutató -> ossza meg velünk a kapcsolatot kapcsolatban a vállalati (található alatt. lépés 2 regisztrálása) közzétevő Namespace-Namespace >]
* **Portok:** [közzétételi portál -> virtuális gépek -> az ajánlat -> Virtuálisgép-rendszerképek lap -> Nyitott portok]
* **Felsorolt termékváltozat(ok) árának meghatározását díjszabás módosítása**
* **Számlázási modell módosítás felsorolt termékváltozat(ok) árának meghatározását**
* **A listán szereplő termékváltozat(ok) árának meghatározását régiók számlázási eltávolítása**
* **Az adatlemezek száma a listán termékváltozat(ok) árának meghatározását módosítása**

## <a name="step-2-set-your-prices"></a>2. lépés: Az árak megadása
### <a name="pricing-models"></a>Díjszabási modellel
| Díjszabási modell | Leírás |
| --- | --- |
| Alapszintű |Vásárláskor fizetett havi átalánydíj Ha például 10 USD havonta. |
| Használat (más néven) használat, a mérőszámok) |Fizetés, az ajánlat közzétevője által definiált. Kerettúllépés nem definiálható Munkaállomásonként / felhasználó, stb., mivel nem lett egy felhasználó vagy a funkció díjalap ehhez töredékéért fogalma nem. Használat a partner által jelentett óránként. Ügyfél fizet az a havi számlázási ciklusban, meghozni hasonló havi csomagok helyett. |
| Ingyenes próbaidőszak |Ügyfél lehet, hogy használata ingyenes, korlátozott ideig, és ezután normál díját kell megfizetni ezt követően. |
| Ingyenes szint |Csomag, mindig ingyenes. |
| Áttelepítés (más néven) átalakítás vagy frissítést/alacsonyabb szintű) csomag |A jelenlegi csomag áthelyezését egy másik elfogadható terv; felhasználó fogalma partner által meghatározott. |

**Díjszabási modellekhez elérhető ajánlat típus szerint**

> [!IMPORTANT]
> Az ajánlattípus rendelkezésre állása az egyes díjszabási modellek változhat. Az alábbi táblázatban talál.
> 
> 

|  | Csak az alap | Csak a használatalapú | Alap + felhasználás |
| --- | --- | --- | --- |
| Virtuálisgép-lemezkép |Nem |Igen |Nem |
| Fejlesztői szolgáltatás |Igen |Igen |Igen |

### <a name="21-set-your-vm-prices"></a>2.1. A VM árak
Jelenleg a virtuális gépek esetében van a következő **háromféle számlázási modellek:**

* **Óránkénti:** Ügyfelek által a kiadók a Virtuálisgép-méretek a díjszabási táblázat alapján óránkénti alapon kell fizetnie. Az **óradíjas** modell a termékváltozatok teljes költsége a szoftver költségét a közzétevő alapján számítjuk fel, a infrastruktúra díjat számítunk fel a Microsoft által a háttéradatok lesz. A teljes költség jelenik meg az ügyfél egy óránkénti és havi díja, a vásárlás kiválasztásakor (lásd az alábbi képernyőképen). **Közzétevő kap 80 %-át szoftver őket alapján számítjuk fel.** Ezért győződjön meg arról, a számítás ennek megfelelően a termékváltozatok árak megadása előtt.
  
    ![rajz](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Ingyenes próbaverzió:** Ez a beállítás akkor egy másik íz óradíjas modell. Itt az ügyfélnek nem kell fizetnie az első 30 days(Free) szoftver költsége a virtuális gép üzembe helyezése után. 30 nap letelte után akkor kell fizetnie az óradíjas modellben a kiadók által meghatározott díjszabás alapján óránkénti alapon.
* **Bring-Your-saját licenc (használata BYOL):** A kiadók kezelheti a virtuális gépen futó szoftver licencelése.

**Fontos:** Miután az ajánlat/SKU szerepel az Azure piactéren, az alább megadott mezők nem módosítható.

* **Felsorolt termékváltozat(ok) árának meghatározását díjszabás módosítása**
* **Számlázási modell módosítás felsorolt termékváltozat(ok) árának meghatározását**
* **A listán szereplő termékváltozat(ok) árának meghatározását régiók számlázási eltávolítása**
* **Az adatlemezek száma a listán termékváltozat(ok) árának meghatározását módosítása**
* **Ajánlat azonosítója:** [közzétételi portál -> virtuális gépek -> Válassza ki az ajánlat -> Virtuálisgép-rendszerképek-ajánlat azonosítója >]
* **Termékváltozat-azonosító:** [közzétételi portál -> virtuális gépek -> Válassza ki az ajánlat -> lap -> termékváltozatok adjon hozzá egy Termékváltozatot]
* **Közzétevő Namespace:** [közzétételi portál Virtual Machines -> lap -> útmutató -> ossza meg velünk a kapcsolatot kapcsolatban a vállalati (található alatt. lépés 2 regisztrálása) közzétevő Namespace-Namespace >]
* **Portok:** [közzétételi portál -> virtuális gépek -> az ajánlat -> Virtuálisgép-rendszerképek lap -> Nyitott portok]

### <a name="sell-to-countries-of-the-sku"></a>"Értékesítési" ország a termékváltozat
Alaposan gondolja át, ahol elérhetővé a termékváltozatok kell. Egyes országok besorolt "Microsoft remit", míg mások vannak besorolva, a "Adófizetéshez."

* "Microsoft remit" országban a Microsoft adók gyűjti össze az ügyfelek, és a kormányzati (kap) adók fizet.
* "ISV fizetünk" országban partnerek felelős adók ügyfelek gyűjtése és az adók és a kormány. Értékesíteni szeretné termékét az országok "ISV fizetünk" lehetőséget választja, ha a funkció alapján számítja ki, és a kiválasztott országokban adók kell fizetnie kell rendelkeznie.

> [!NOTE]
> A Termékváltozat nem lesz elérhető a országokban, kivéve, ha állítsa be a díjszabás a [közzétételi portál](https://publish.windowsazure.com). Díjszabása óránként és BYOL termékváltozatok beolvasása beállításához útmutatást alább.
> 
> 

### <a name="211-how-to-set-up-hourly-pricing-model-for-a-sku"></a>2.1.1 hogyan állítható be óránként díjszabási modell egy termékváltozat
Díjszabási modell egy termékváltozat óránként beállításához az alábbiakban megadott lépéseket követve:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).
2. Keresse meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **Termékváltozatok** fülre.
4. Győződjön meg arról, hogy a Termékváltozat "Óránkénti számlázási modell" van megjelölve. Ha nem, majd kattintson a a **szerkesztése** gombra a a számlázási modellt. Megnyílik egy ablak. Törölje a jelet a jelölőnégyzetből "számlázási és licencelési történik külsőleg (más néven a saját licenc használatát) Azure-ból", és mentse a módosításokat.
5. A Termékváltozat központi telepítés az első 30 napban ingyenes próbaverzió engedélyezni szeretné, ha a választhatja "" egy "Month" a kérdést a "Érhető el egy ingyenes próbaverzióra?" Ellenkező esetben válassza a "Nem próbaverzió". Most már kövesse az alábbi lépéseket.
6. A bal oldali menüben kattintson a **DÍJSZABÁSI** fülre.
7. Válassza ki az alapszintű régiót.
   
   ![rajz](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Állítsa be az összes magon árak. *Adja meg a díj az összes mag, a Termékváltozat akkor is, ha a Termékváltozat nem támogatja ezt.*
   
    ![rajz](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Manuálisan állítsa be az árak a többi régió esetében, vagy a AUTOPRICE varázsló segítségével állítsa be az árak egyéb régiók, az alap régió alapján. A AUTOPRICE varázslóban kattintson a gombra használandó **AUTOPRICE egyéb piacok alapú ON árak az Egyesült Államok.** **Megjegyzés:** A gomb címkéje a kiválasztott régiójától függően eltérő lehet. Mivel ez a dokumentum létrehozása során azt kiválasztott Egyesült Államok, ezért a gomb címkéje "Automatikus díjszabása más piacokon, az Egyesült Államokban díjak alapján" az alábbi képernyőfelvételen látható módon.
   
   ![rajz](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. Az automatikus ár varázsló nyílik meg. Az első oldal megjelenítik a kiválasztható alap piacra. Győződjön meg a szakaszt, és a "->" gombra kattintva léphet a következő lapra.
    
    ![rajz](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. A beállítás kiválasztásakor a magok és a tervek 2 oldalon jelennek meg. Válassza ki a kívánt csomagokat, és kattintson a "->" gombra. Kattintson a **váltógomb összes** gombra kattintva válassza ki az összes a **szolgáltatási csomagok** és **mérőszámok** vagy manuálisan ellenőrizheti a jelölőnégyzeteket. **Meg kell adnia ára az összes mag, a Termékváltozat akkor is, ha a Termékváltozat nem támogatja ezt.** Ezért győződjön meg arról, hogy az összes mag méretek vannak-e jelölve.
    
    ![rajz](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. 3. oldal megjeleníti a piacok és régióban. Kattintson a **váltógomb összes** gombra, válassza ki az összes régióban, vagy manuálisan jelölőnégyzetekből régióhoz. Kattintson a "->" gombra kattintva léphet a következő lapra. **Megjegyzés:** A Microsoft adó elengedik országok szimbólum például egy adott jelöli. További részletekért tekintse meg a szakasz "értékesítési" ország ezt oldal a termékváltozat.
    
    ![rajz](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. 4. oldal megjeleníti az átváltási árfolyamok. Kattintson a Befejezés gombra a lépések végrehajtásához.

### <a name="212-how-to-set-up-byol-pricing-model-for-a-sku"></a>2.1.2 hogyan állítható be BYOL modell a Termékváltozat díjszabása
BYOL modell a Termékváltozat díjszabása beállításához az alábbiakban megadott lépéseket követve:

1. Jelentkezzen be a [közzétételi portál](https://publish.windowsazure.com).
2. Keresse meg a **virtuális gépek** lapra, és válassza az ajánlatot.
3. A bal oldali menüben kattintson a **Termékváltozatok** fülre.
4. Győződjön meg arról, hogy a Termékváltozat "Használata a saját licenc Termékváltozat" van megjelölve. Ha nem, majd kattintson a SZERKESZTÉS gombra, a számlázási modell vissza. Megnyílik egy ablak. Jelölje be a jelölőnégyzetet a "számlázási és licencelési történik külsőleg (más néven a saját licenc használatát) Azure-ból", és mentse a módosításokat.
   
   ![rajz](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. A bal oldali menüben kattintson a **DÍJSZABÁSI** fülre.
6. Válassza ki a kiindulási régiót, és elérhetővé a Termékváltozat a régióban EXTERNALLY-LICENSED (BYOL) Termékváltozat rendelkezésre ÁLLÁSI csoportban a Termékváltozat be a ellenőrzésével (lásd az alábbi képernyőképen).
   
   ![rajz](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. Győződjön meg arról, a Termékváltozat érhető el a más régiókban manuálisan, vagy a AUTOPRICE varázslót használhatja erre a célra. Tekintse meg a pontok #9, #13 (amely azt ismerteti, hogy a AUTOPRICE varázsló használata) szakaszában **"2.1.1 díjszabási modell egy termékváltozat óránként beállítása"** ezt oldal.

### <a name="22-set-your-developer-service-prices"></a>2.2. A fejlesztői szolgáltatás árak
Csomagok alap + felhasználás, ahol alapja a havi díjak pedig kerettúllépési a használatalapú fizetés ár tetszőleges kombinációja lehet. (Lásd alább a részleteket.)

**Példa**  Contoso fejlesztői szolgáltatásajánlat

| Felkészülés | Ár | Tartalmazza | Áttelepítés |
| --- | --- | --- | --- |
| Ingyenes |0 USD/hónap |Alapvető funkciókat. |Bármilyen egyéb csomag helyezheti át |
| Bronz |10 USD/hónap |Alapvető funkciókat és funkció X 1000-kvótát. |Áttelepíthetők a bronz emellett Silver és Gold tervek |
| Bronz Plus |Ingyenes próbaidőszak: 0 USD/hónap + 0 USD/meter01 |Alapvető funkciókat és egy legfeljebb 10 000 X funkció kvótáját.  Miután funkció X kvóta használja, az ügyfél is szerinti fizetés meter01 keresztül. |Ezüst és és Gold tervek helyezheti át |
| Bronz Plus |Fizetős időszak (más néven) az ingyenes próbaverzió lejárt): 10 USD/hónap + $ 0,05/meter01 |Alapvető funkciókat és egy legfeljebb 10 000 X funkció kvótáját.  Miután funkció X kvóta használja, az ügyfél is szerinti fizetés meter01 keresztül. |Ezüst és és Gold tervek helyezheti át |
| Silver |$ 0,15/meter01 |Az ügyfél képes szerinti fizetés meter01, amely X funkció segítségével. |Áttelepíthetők a bronz és Gold tervek |
| Silver szintű Plus |havi 20 $ + $ 0,15/meter01 + $ 0,01/meter02 |Alapvető funkciókat és a egy legfeljebb 10 000 X szolgáltatás és funkció Y 100 kvótáját.  Miután a szolgáltatás X kvóta használja, az ügyfél is szerinti fizetés meter01 keresztül.  Miután a szolgáltatás Y kvóta használja, az ügyfél is szerinti fizetés meter02 keresztül. |Áttelepíthetők a bronz Plus és Gold tervek |
| Arany |1000/hó |Kvóta legfeljebb 10 000 X, Y, funkció 1000 szolgáltatás és funkció Z korlátlan. |Helyezheti át az ingyenes kivételével minden csomag |

## <a name="step-3-provide-support-information"></a>3. lépés: Adja meg a támogatási információk
A kapcsolattartási adatait csak a partner és a Microsoft belső kommunikációs szolgálnak. A támogatási URL-CÍMÉT a végfelhasználók számára elérhető lesz.

1. Nyissa meg a **támogatási** fejléc a közzétételi portál bal oldalán.
2. Adja meg az adatokat a **Engineering Contact**.
3. Adja meg az adatokat a **ügyfél-támogatási**. Ha csak e-mailes támogatást ad meg, adjon meg egy helyőrző telefonszámot, és a megadott e-mail címet fogja használni.
4. Adja meg a támogatási URL-CÍMÉT.

## <a name="step-4-choose-azure-marketplace-categories"></a>4. lépés: Válassza ki az Azure Marketplace-kategóriák
A **kategóriák** lap tartalmaz beállításokat tömbjét. Legfeljebb öt kategóriák választhat.

## <a name="how-your-marketing-will-appear"></a>Hogyan jelenik meg a marketing
Hogyan használja fel az ajánlatra, marketing-információ részletes nézete az alábbi, az a [Azure Marketplace webhelyén](https://azure.microsoft.com/marketplace/) és a a [az Azure portal](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Az Azure piactér webhelyén
![rajz](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![rajz](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Az Azure Marketplace webhelyén ajánlatok listája*

![rajz](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Az ajánlat az Azure Marketplace webhelyén leírás részletei*

![rajz](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Ajánlat díjszabásáról az Azure Marketplace webhelyén leírása*

### <a name="azure-portal"></a>Azure Portal
![rajz](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Az Azure Portalon ajánlatok listája*

![rajz](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Az ajánlat leírása részletei az Azure Portalon*

## <a name="next-steps"></a>További lépések
Most, hogy a Marketplace-tartalom betöltése hozzunk folytatná az ajánlat tesztelése az átmeneti állapotában is. Azonban választania kell a megfelelő ajánlat típusát a listából, az alábbi módon szükséges lépések eltérhetnek az ajánlat típus szerint.

* [A Virtuálisgép-ajánlat tesztelje az előkészítés](marketplace-publishing-vm-image-test-in-staging.md)
* [A megoldás sablon ajánlat tesztelése az átmeneti állapotában](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Lásd még
* [Első lépések: Ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
