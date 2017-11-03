---
title: "Készítse elő, az ajánlatot az Azure piactérről való központi telepítésének tesztelése |} Microsoft Docs"
description: "Részletes útmutatást nyújtó tartalom marketing, árképzési tervek konfigurálása és tesztelt az ajánlatot, mielőtt telepítené az Azure piactérről."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: hascipio
ms.openlocfilehash: 7db86716cdf8f9eb921c3c1813970acae7a3016b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Marketing tartalmú ajánlat létrehozásának befejezéséhez
Ebben a lépésben a közzétételi folyamat meg kell adnia bizonyos marketing tartalom és a ajánlat és/vagy az Azure piactéren termékváltozatok részleteit. Például fog adja meg a termékhez, vállalati emblémát, ár tervek, tervek részleteit és más szükséges az ajánlat és/vagy SKU leküldése átmeneti adatok leírását. Ezt az információt használja az Azure portálon marketing tartalmat. Ez a folyamat kezdve látni fogja a [közzétételi portáljára][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>1. lépés: Adja meg a tartalom marketing piactér
**Angol az alapértelmezett, és csak a támogatott nyelven.** Győződjön meg arról, hogy a mezők található összes információk angol nyelven. Az átmeneti üzem megkezdéséig az adatokat bármikor módosíthatja.

1. Nyissa meg a közzétételi portálra [https://publish.windowsazure.com](https://publish.windowsazure.com).
2. A bal oldali menüben kattintson a **Marketing** fülre.
3. A fő panelen kattintson a **angol (amerikai)** gombra.
   
   > [!IMPORTANT]
   > Minden mező bejegyzéseket, beleértve a képeket, meg tudják átmeneti leküldése kell rendelkeznie.
   > 
   > 

### <a name="details-and-plans"></a>Részletek és tervek
1. Adja meg az ajánlat címe (legfeljebb 50 karakter), összefoglalás (legfeljebb 100 karakter) kínálnak, (legfeljebb 256 karakter) hosszú összefoglalás kínálnak, ajánlat leírása (legfeljebb 1300 karakter), emblémák alatt a **részletek** lap
2. Adja meg a terv cím (legfeljebb 50 karakter), terv összegzés (legfeljebb 100 karakter), a terv leírása (legfeljebb 2000 karakter) alatt a **tervek** fülre.
   
   > [!NOTE]
   > Az alábbi HTML-címkék formázásához az összefoglalást, hosszú összegzése és ajánlat és tervek leírását is használhatja. Az engedélyezett HTML-kódok a következők h1, h2, h3, h4, h5, p, ol, ul, li, a [cél |} href], erős, em, a b i.
   > 
   > 
3. Ne adjon meg ismétlődő szöveg alatt ajánlat és a terv leírását.
4. Ne adjon meg ismétlődő szöveg tervnek cím és ajánlat hosszú összegzése alatt.
5. Nem adja meg a terv címét ismétlődő szöveget és rövid összefoglalása.
6. Az ajánlat, amely több tervek azonos terv címek nem adja meg.
7. A kötelező specifikációnak (a közzétételi Portáljára említett) a képek feltöltése PNG formátumban, egy a minden méretét.
8. Győződjön meg arról, hogy az egyes emblémák kövesse az alábbiakban leírt Azure piactér emblémáinak használatáról.
   
   ![rajz](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Az Azure piactér emblémáinak használatáról**

A közzétételi Portáljára feltöltött összes emblémák kövesse az alábbi irányelveket:

* Az Azure terv egy egyszerű színpaletta rendelkezik. Továbbra is a számát az elsődleges és másodlagos színt a az embléma alacsony.
* Az Azure portál a témának színeket fehér és fekete. Ezért kerülje ezeket a színeket, a emblémák hátterének színét. Néhány színt, amely az Azure portálon jól láthatóan elhelyezett tenné a emblémák használja. Azt javasoljuk, hogy egyszerű alapszínek. **Ha átlátszó háttérrel használ, majd ellenőrizze, hogy a emblémák szöveg nem fehér vagy fekete vagy kék.**
* Ne használjon egy színátmenetes hátterének az emblémának.
* Ne helyezzen szöveg, még akkor is a vállalat vagy a márka neve, az embléma. Az embléma megjelenését és működését "egyszerű" kell, és átmenetek kerülendő.
* Az embléma nem kell felhőbe.
* Kis embléma 40 X 40 méretűnek kell lenniük képpont
* Közepes méretű embléma 90 X 90 méretűnek kell lenniük képpont
* Nagy embléma 115 X 115 méretűnek kell lenniük képpont
* Széles embléma 255 X 115 méretűnek kell lenniük képpont
* Hero embléma 815 X 290 méretűnek kell lenniük képpont

> [!NOTE]
> A kiemelt embléma nem kötelező megadni. A közzétevő nem szeretné kiemelt embléma feltöltéséhez. Azonban egyszer feltöltött hero ikon nem lehet törölni a közzétételi portálon. Idő lejárta után a partner Hero ikonok az Azure piactér irányelveket kell követnie.
> 
> 

  ![rajz](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**További útmutatást a következő Hero embléma ikon (nem kötelező)**

* A kiemelt embléma nem kötelező megadni. A közzétevő nem szeretné kiemelt embléma feltöltéséhez. **Azonban egyszer feltöltött hero ikon nem lehet törölni a közzétételi portálon. Ekkor a partner kell útmutatást Azure piactér kiemelt ikonok más ajánlatot nem hagyható jóvá üzemi környezetben.**
* A közzétevő megjelenített név, a terv jogcímre és a hosszú összefoglalás ajánlat fehér betűszíne jelenik meg. Ezért ne bármely könnyű szín megőrzi a kiemelt ikon a háttérben. Fekete, fehér és átlátható háttér nem engedélyezett hőse ikonok.
* A közzétevő megjelenített név, cím, az ajánlat hosszú összegzése és a Létrehozás gombra vannak ágyazva programozott módon a kiemelt embléma után az ajánlat kerül terv felsorolt. Ezért kell meg szöveget a kiemelt embléma tervezésekor. Ne változtassa meg üres területet a jobb oldalon, mert a szöveg (pl. közzétevő megjelenítendő név terv cím hosszú összefoglalás ajánlat) lesznek a tagjai programozott módon lépünk Önnel ott keresztül. A szöveg üres területet kell lennie a jobb oldali 415 x 100 (és a balról 370px ellensúlyozza).
  
  ![rajz](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Hivatkozások
Az a **hivatkozások** a bal oldali sávon lapra, adja meg az esetleges hivatkozások, amelyek az ügyfelek adatokkal. Minden hivatkozás esetében adjon meg egy nevet és egy URL-címet.

![rajz](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>A minta lemezképeket (választható)
> [!NOTE]
> Többek között a minta-lemezkép (nem kötelező).
> Annak ellenére, hogy a közzétételi minta képeit feltöltheti portálon (a rendszer véletlenszerűen kiválasztott) csak egy kép lekérdezi jelenik meg az Azure portálon. Ezért azt javasoljuk, legfeljebb egy minta lemezkép feltöltése.
> 
> 

Az a **minta képek** a bal oldali menüben lapján töltse fel az új képet kattintva **új lemezkép feltöltése**. Ha egy meglévő képet, és cserélje le azt, kattintson a kívánt **csere kép**.

![rajz](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Jogi tudnivalók
Az a **jogi** lapján adjon meg egy hivatkozást, a házirendek/használati feltételeket. Írja be vagy illessze be a feltételeket a nagy **használati** mezőbe. A jogi használati feltételeket karakter határa 1 000 000 karakternél.

![rajz](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Megjegyzés:** a virtuális gép ajánlatok esetén az ajánlat/SKU elő van készítve az Azure portálon, már nem módosíthatja a mezőket az alábbi:

* **Ajánlat azonosítója:** [közzétételi portal -> a virtuális gépek -> az ajánlat Virtuálisgép-rendszerképek -> lapon -> az ajánlat azonosítója]
* **SKU-azonosítója:** [közzétételi portal -> a virtuális gépek -> Válassza ki az ajánlatot -> lapon -> termékváltozatok hozzáadása egy SKU]
* **Közzétevő Namespace:** [közzétételi portal -> a virtuális gépek -> lapon -> Tell Us megemlítik a vállalatot (található a "Lépés 2 regisztrálása a vállalati") -> forgatókönyv Publisher Namespace Namespace ->]

A virtuális gép ajánlatok esetén az ajánlat/SKU szerepel az Azure piactéren már nem módosíthatja a mezőket az alábbi:

* **Ajánlat azonosítója:** [közzétételi portal -> a virtuális gépek -> Válassza ki az ajánlatot -> Virtuálisgép-lemezképek -> az ajánlat azonosítója]
* **SKU-azonosítója:** [közzétételi portal -> a virtuális gépek -> Válassza ki az ajánlatot -> lapon -> termékváltozatok hozzáadása egy SKU]
* **Közzétevő Namespace:** [közzétételi portal -> a virtuális gépek -> forgatókönyv lapon -> Mondja el nekünk kapcsolatban a vállalati (található alatt lépés 2 regisztrálása) Publisher Namespace Namespace ->]
* **Portok:** [közzétételi portal -> a virtuális gépek -> az ajánlat -> Virtuálisgép-rendszerképek lap megnyitása ->]
* **A felsorolt SKU(s) árképzési módosítása**
* **Számlázási modell megváltozott felsorolt SKU(s)**
* **A felsorolt SKU(s) régiói számlázási törlése**
* **A felsorolt SKU(s) adatlemez módosítása**

## <a name="step-2-set-your-prices"></a>2. lépés: Az árak beállítása
### <a name="pricing-models"></a>Árképzési modellekkel
| Díjszabási modell | Leírás |
| --- | --- |
| Alapszintű |Egyszerű havi arány fizetős vásárláskor; például $10/ hónap. |
| Felhasználás (más néven használati, mérő) |Nagy használati, az ajánlat közzétevője által definiált. – Kerettúllépés nem definiálható Munkaállomásonként, felhasználó, stb., /, amíg nem ismerik a felhasználó vagy a funkció proration ehhez töredéke alatt. Használati a partner által jelentett óránként. Ügyfél fizet a havi számlázási ciklus, előre hasonló havi tervek szemben. |
| Ingyenes próbalehetőség |Ügyfél szabad, adott időtartamra használhatja, illetve majd ezt követően kell fizetnie normál díj fizetendő. |
| Ingyenes szint |Terv a szabad. |
| Áttelepítés (más néven átalakítás vagy frissítést/alacsonyabb szintre való visszalépést) terv |A felhasználó az aktuális terv áthelyezése egy másik elfogadható terv; fogalma partner határozza meg. |

**Az ajánlattípus által elérhető modellek díjszabása**

> [!IMPORTANT]
> Az ajánlattípus eltérők bizonyos árképzési modellt rendelkezésre állását. Az alábbi táblázatban találja.
> 
> 

|  | Csak alapja | Csak a fogyasztás | Alap + fogyasztás |
| --- | --- | --- | --- |
| Virtuálisgép-lemezkép |Nem |Igen |Nem |
| Fejlesztői szolgáltatás |Igen |Igen |Igen |

### <a name="21-set-your-vm-prices"></a>2.1. A virtuális gép árak
Jelenleg a virtuális gépek kell a következő **3 típusú számlázási modellek:**

* **HOURLY:** ügyfelek beolvasása felszámított alapján állítja be a Virtuálisgép-méretek a közzétevők a díjszabás óránként alapon. Esetében **óránkénti számlázási** modell az SKU a teljes ár lesz a szoftver által a közzétevő és infrastruktúra költsége Microsoft által a háttéradatok. A teljes költség jelenik meg az ügyfél egy óránkénti és havi ár azok van annak eldöntéséhez, hogy a beszerzési (lásd az alábbi képernyőképen látható). **Közzétevő megkapja a 80 %-át szoftver által őket.** Ezért kérjük, ellenőrizze a termékváltozatok az árak a számítás beállítása előtt megfelelően.
  
    ![rajz](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Ingyenes próbaverzió:** Ez az óránkénti modell egy másik kiadása. Itt az ügyfél nem get szó, a szoftver az első 30 days(Free) költsége a virtuális gép telepítését követően. 30days után azok beolvasása felszámított az óránkénti modell közzétevők által beállított díjszabás alapján óránként alapon.
* **Bring Your-saját-licencet (BYOL):** a közzétevők kezelheti a virtuális Gépen futó szoftver licencelési.

**Fontos:** az ajánlat/SKU szerepel, az Azure piactérről, már nem módosíthatja az alábbi mezőket.

* **A felsorolt SKU(s) árképzési módosítása**
* **Számlázási modell megváltozott felsorolt SKU(s)**
* **A felsorolt SKU(s) régiói számlázási törlése**
* **A felsorolt SKU(s) adatlemez módosítása**
* **Ajánlat azonosítója:** [közzétételi portal -> a virtuális gépek -> Válassza ki az ajánlatot -> Virtuálisgép-lemezképek -> az ajánlat azonosítója]
* **SKU-azonosítója:** [közzétételi portal -> a virtuális gépek -> Válassza ki az ajánlatot -> lapon -> termékváltozatok hozzáadása egy SKU]
* **Közzétevő Namespace:** [közzétételi portal -> a virtuális gépek -> forgatókönyv lapon -> Mondja el nekünk kapcsolatban a vállalati (található alatt lépés 2 regisztrálása) Publisher Namespace Namespace ->]
* **Portok:** [közzétételi portal -> a virtuális gépek -> az ajánlat -> Virtuálisgép-rendszerképek lap megnyitása ->]

### <a name="sell-to-countries-of-the-sku"></a>Az SKU "Értékesít-az" ország
Alaposan gondolja át, ahol elérhetővé a termékváltozatok kell. Egyes országokban besorolt "Microsoft átutalási", míg mások besorolva, "ISV átutalási."

* "Microsoft átutalási" országokban Microsoft adók gyűjt az ügyfelektől, és a kormányzati (kap) adók fizet.
* "ISV eljár" országokban a partnerek olyan adó-vevők összegyűjtése és adók fizető kormánya a felelős. Ha "ISV eljár" országokban eladásra, képes kiszámításához és adók választja országban kell fizetnie kell rendelkeznie.

> [!NOTE]
> A Termékváltozat nem lesz elérhető a országokban kivéve, ha meg az árképzési a [portal közzétételi](https://publish.windowsazure.com). Első beállítása óránként és BYOL termékváltozatok árképzési iránymutatást alatt.
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 hogyan óránkénti árképzési modellt állíthatja a termékváltozat
Kövesse a telepítési óránkénti árképzési modellt a termékváltozat alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).
2. Keresse meg a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **Termékváltozatok** fülre.
4. Győződjön meg arról, hogy az SKU "Óránkénti számlázási modell" jelölésű. Ha nem, majd kattintson a a **szerkesztése** gombra a számlázási modelljét. Ekkor megnyílik egy ablak. Törölje a jelet a jelölőnégyzetből "számlázási és licencelési történik külsőleg (más néven a saját licenc) az Azure-ból", és mentse a módosításokat.
5. Termékváltozat-telepítés az első 30days ingyenes engedélyezni szeretné, ha a választhatja "Egy hónap" a kérdés "Érhető el egy ingyenes próbaverzióra?" Amennyiben nem válassza a "Nem próbaverzió" lehetőséget. Most kövesse az alábbi lépéseket.
6. A bal oldali menüben kattintson a **ÁRAZÁS** fülre.
7. Válassza ki a kiinduló régiót.
   
   ![rajz](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Állítsa be az összes mag árakat. **Meg kell adnia ár egy metódust az összes mag akkor is, ha a Termékváltozat nem támogatja ezt.**
   
    ![rajz](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Állítsa be manuálisan a árakat a más régiókból, vagy a AUTOPRICE varázsló segítségével más régiók az alap régió alapján árak. A AUTOPRICE varázslóban kattintson a gombra használandó **AUTOPRICE egyéb piacok alapú ON árak része az Egyesült Államokban.** **Megjegyzés:** a gomb címkéje a régió, amely a kijelölt függően eltérő lehet. Mivel ez a dokumentum létrehozása során azt is kijelölt az Amerikai Egyesült Államok, így a gomb címkéje "Automatikus ár alapján az Amerikai Egyesült Államokban árak piacokra" alábbi képernyőképen látható módon.
   
   ![rajz](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. Az automatikus ár varázsló nyílik meg. Az első lap a telepítendő alap piaci jeleníti meg. A szakaszban ellenőrizze, és a "->" gombra kattintva helyezze át a következő oldalra.
    
    ![rajz](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. A magok és tervek kiválasztását 2 lapon jelenik meg. Jelölje ki a kívánt terveket, majd kattintson a "->" gombra. Kattintson a **váltása összes** gombra kattintva válassza ki az összes a **tervek szolgáltatás** és **mérőszámok** vagy manuálisan lehessen ellenőrizni a jelölőnégyzeteket. **Meg kell adnia ár egy metódust az összes mag akkor is, ha a Termékváltozat nem támogatja ezt.** Ezért győződjön meg arról, hogy az alapvető méretek vannak-e jelölve.
    
    ![rajz](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. 3. oldal a piacon és régiókban jeleníti meg. Kattintson a **váltása összes** gombra, jelölje ki minden egyes, vagy manuálisan négyzeteket régióban. Kattintson az "->" gombra kattintva léphet a következő lapra. **Megjegyzés:** Microsoft adó engedni országok egy házat, például a szimbólum jelöli. További részletekért tekintse meg a metódust a lap egyes országokra szakasz "Értékesít-a következőnek".
    
    ![rajz](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. Lap 4 árfolyam jeleníti meg. Kattintson a Befejezés gombra a műveletek végrehajtásához.

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 a telepítő BYOL árképzési modellt a termékváltozat hogyan
Hajtsa végre egy termékváltozat fizetési modell BYOL beállítása alábbi lépéseket:

1. Jelentkezzen be a [portal közzétételi](https://publish.windowsazure.com).
2. Keresse meg a **virtuális gépek** fülre, és válassza ki az ajánlatot.
3. A bal oldali menüben kattintson a **Termékváltozatok** fülre.
4. Győződjön meg arról, hogy a Termékváltozat nevén a"saját licenc SKU" van megjelölve. Ha nem, majd kattintson a SZERKESZTÉS gombon visszaállítani a számlázási modellt. Ekkor megnyílik egy ablak. Jelölje be a jelölőnégyzetet a "számlázási és licencelési történik külsőleg (más néven a saját licenc) az Azure-ból", és mentse a módosításokat.
   
   ![rajz](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. A bal oldali menüben kattintson a **ÁRAZÁS** fülre.
6. Válassza ki a kiinduló régiót, és elérhetővé az SKU régióban EXTERNALLY-LICENSED (BYOL) SKU rendelkezésre ÁLLÁSI csoportban lévő Termékváltozat szemben a jelölőnégyzet bejelölésével (lásd az alábbi képernyőképen látható).
   
   ![rajz](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. A Termékváltozat elérhetővé tétele a más régiókban manuálisan, vagy használhatja a AUTOPRICE varázslót erre a célra. Tekintse meg a pontok #9-#13 (amely azt ismerteti, hogy a AUTOPRICE varázsló használata) című **"2.1.1 hogyan állíthatja fizetési modell egy termékváltozat óránként"** a lap.

### <a name="22-set-your-developer-service-prices"></a>2.2. A fejlesztői szolgáltatás árak
Tervek talál + fogyasztás, ahol alapja a havi díjakon és keretét a fizetési használati ár tetszőleges kombinációja lehet. (Lásd alább olvashat.)

**Példa:** Contoso fejlesztői szolgáltatásajánlat

| Felkészülés | Ár | Tartalmazza | Áttelepítési útvonal |
| --- | --- | --- | --- |
| Ingyenes |$0/ hónap |Alapvető funkciókat. |Bármely más terv telepítheti át |
| Bronz |$10/ hónap |Alapvető funkciókat és a következő szolgáltatás X 1000 tartozó kvóta. |Telepítheti át a bronz Plus ezüst és arany tervek |
| Bronz plusz |Ingyenes próbaverzió lejárta: 0/ hónap + $0/meter01 |Alapvető funkciókat és 10 000-re a következő szolgáltatás X tartozó kvóta.  Szolgáltatás X kvóta szolgál, ha az ügyfél használati meter01 keresztül is nagy. |Telepítheti át a ezüst Plus és az arany tervek |
| Bronz plusz |Fizetett időszak (más néven ingyenes próbaverzió lejárt): $10/hónap + $ 0,05/meter01 |Alapvető funkciókat és 10 000-re a következő szolgáltatás X tartozó kvóta.  Szolgáltatás X kvóta szolgál, ha az ügyfél használati meter01 keresztül is nagy. |Telepítheti át a ezüst Plus és az arany tervek |
| Ezüst |$ 0,15/meter01 |Az ügyfél használati meter01, amely szolgáltatás X keresztül is nagy. |Telepítheti át a bronz és arany tervek |
| Ezüst plusz |$20/ hónap + $ 0,15/meter01 + $ 0,01/meter02 |Alapvető funkciókat és 10 000-re a következő szolgáltatás X és Y funkció 100 tartozó kvóta.  Miután a szolgáltatás X kvóta használata esetén az ügyfél használati meter01 keresztül is fizet.  Miután a szolgáltatás Y kvóta használata esetén az ügyfél használati meter02 keresztül is fizet. |Telepítheti át a bronz Plus és az arany tervek |
| Arany |1000/hónap |A következő szolgáltatás X, Y, funkció 1000 10 000-re vonatkozó kvótáját és korlátlan funkció z-ig. |Telepítheti át a szabad karakterek kivételével minden tervek |

## <a name="step-3-provide-support-information"></a>3. lépés: Adja meg a támogatási információk
A kapcsolattartási adatait kommunikációhoz használt belső a partnerek és a Microsoft között csak. A támogatási URL-címet a záró használó ügyfelek számára elérhető lesz.

1. Lépjen a **támogatási** címsor a közzétételi portál bal oldalán.
2. Adja meg az adatokat **mérnöki forduljon**.
3. Adja meg az adatokat **ügyfél-támogatási**. Ha csak az e-mailes támogatást ad meg, adjon meg egy üres telefonszámot, és a megadott e-mail címét használja helyette.
4. Adja meg a támogatási URL-CÍMÉT.

## <a name="step-4-choose-azure-marketplace-categories"></a>4. lépés: Jelölje ki az Azure piactér kategóriák
A **kategóriák** lapon megadott beállítások tömbje biztosít. Az ezekkel az ajánlatot tartozhatnak, és válasszon legfeljebb öt kategóriába.

## <a name="how-your-marketing-will-appear"></a>Hogyan fog megjelenni a marketing
Alábbiakban található információkat marketing ajánlatot felhasznált részletes nézete a a [Azure piactér webhely](https://azure.microsoft.com/marketplace/) és a [Azure-portálon](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Az Azure piactér webhely
![rajz](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![rajz](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Az Azure piactér webhelyen ajánlatok listája*

![rajz](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Az ajánlat részleteinek az Azure piactér webhelyen*

![rajz](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Ajánlat az Azure piactér webhelyen díjszabása leírása*

### <a name="azure-portal"></a>Azure Portal
![rajz](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Az Azure portálon ajánlatok listája*

![rajz](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Az ajánlat részleteinek az Azure-portálon*

## <a name="next-steps"></a>Következő lépések
Most, hogy a piactér tartalom be van töltve, most előre együtt mozognak átmeneti ajánlatát tesztelése. Azonban ki kell választani a megfelelő ajánlat típusát az alábbi listán, mivel szükséges lépések eltérhetnek az ajánlattípus által.

* [A tesztelési virtuális gép ajánlatát tesztelése](marketplace-publishing-vm-image-test-in-staging.md)
* [A megoldás sablon ajánlat tesztelése az átmeneti](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Lásd még:
* [Első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
