---
title: HTTPS egyéni Azure CDN-tartomány konfigurálása |} Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti vagy tilthatja le a HTTPS, az Azure CDN-végpont egyéni tartomány.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: rli; v-deasim
ms.openlocfilehash: ca3dad18973197f63e69e6568b8ea5988b279e01
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="configure-https-on-an-azure-cdn-custom-domain"></a>HTTPS egyéni Azure CDN-tartomány konfigurálása

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Az Azure Content Delivery Network (CDN) egy egyéni tartomány a CDN-végpont a HTTPS protokollt támogatja. A HTTPS protokoll használatával az egyéni tartomány, akkor győződjön meg arról, hogy a bizalmas adatokat a rendszer biztonságosan keresztül SSL-titkosítást, amikor az interneten keresztül továbbítja azokat. HTTPS biztosít a megbízható, hitelesítéshez, és védelmet nyújt a webalkalmazások a támadások ellen. Ezenkívül a biztonságos tartalom továbbítása a saját tartománynév használatával (például https:\//www.contoso.com). A munkafolyamat HTTPS engedélyezése az egyszerűsített egy kattintással engedélyezése és a teljes tanúsítványkezelés, minden további költség nélkül.

Az Azure CDN is támogatja a HTTPS PROTOKOLLT a CDN-végpont állomásnevéhez, alapértelmezés szerint. Például, ha a CDN-végpont létrehozása (például a https:\//contoso.azureedge.net), automatikusan engedélyezi a HTTPS PROTOKOLLT.  

A következő fontos attribútumokat a HTTPS-funkció a következők:

- További költség nélkül: a tanúsítvány-beszerzéssel vagy megújítása nem költségekkel és a HTTPS-forgalmat további költség nélkül. Csak fizetnie GB kilépő a CDN.

- Egyszerű engedélyezése: egy kattintással kiépítés érhető el a [Azure-portálon](https://portal.azure.com). REST API vagy egyéb fejlesztői eszközök segítségével is engedélyezze a szolgáltatást.

- Fejezze be a Tanúsítványkezelő: az összes tanúsítvány beszerzésének és kezelik a kulcsokat meg. A tanúsítványok automatikusan kiépítése és újul lejárati, amely eltávolítja a szolgáltatás megszakítás miatt a tanúsítvány lejárati ideje kockázatát.

>[!NOTE] 
>Mielőtt engedélyezné a HTTPS PROTOKOLLT támogatja, akkor kell már létrehozott egy [egyéni Azure CDN-tartomány](./cdn-map-content-to-custom-domain.md).

## <a name="enabling-https"></a>HTTPS engedélyezése

Az egyéni tartománynév a HTTPS engedélyezéséhez kövesse az alábbi lépéseket:

### <a name="step-1-enable-the-feature"></a>1. lépés: A funkció engedélyezése 

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure CDN Standard verizon** vagy **verizon Azure CDN Premium** CDN-profilt.

2. A végpontok, kattintson az egyéni tartomány tartalmazó végpont.

3. Kattintson az egyéni tartománynak a HTTPS engedélyezéséhez.

    ![Egyéni tartományok listája](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Kattintson a **a** ahhoz, hogy a HTTPS, majd kattintson a **alkalmaz**.

    ![Az egyéni tartomány HTTPS állapota](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>2. lépés: A tartomány ellenőrzése

>[!NOTE]
>Ha egy tanúsítvány hatóság engedélyezési (CAA) rekordot a DNS-szolgáltatónál, DigiCert tartalmaznia kell egy érvényes hitelesítésszolgáltatóként. CAA rekord lehetővé teszi, hogy a tartomány tulajdonosai számára adja meg a DNS-szolgáltatókkal a tartományához tartozó tanúsítványokat kibocsátó hitelesítésszolgáltató engedélyezett. Ha a hitelesítésszolgáltató nem szerepel az engedélyezett kiállítóként egy hitelesítésszolgáltatóhoz egy ahhoz, hogy egy tanúsítványt, amely egy CAA rekordja, az adott tartomány vagy altartomány a tanúsítványt kiállító tilos. CAA rekordok kezelésével kapcsolatos információkért lásd: [kezelése CAA rekordok](https://support.dnsimple.com/articles/manage-caa-record/). Egy CAA rekord eszközt, lásd: [CAA rekord segítő](https://sslmate.com/caa/).

#### <a name="custom-domain-is-mapped-to-cdn-endpoint"></a>Az egyéni tartomány van leképezve a CDN-végpont

A végpontra mutató egyéni tartományhoz hozzáadásakor létrehozott egy CNAME rekordot a tartományregisztrálónál hozzárendelése a CDN-végpont állomásnevéhez a DNS táblájában. A CNAME rekord még létezik, és nem tartalmaz a cdnverify altartomány, ha a DigiCert hitelesítésszolgáltatót (CA) használ az egyéni tartomány tulajdonjogát érvényesítéséhez. 

A CNAME rekordot a következő formátumban kell megadni ahol *neve* az egyéni tartománynév és *érték* van a CDN-végpont állomásnevéhez:

| Name (Név)            | Típus  | Érték                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

A CNAME-rekordok kapcsolatos további információkért lásd: [a CNAME DNS-rekord létrehozása](https://docs.microsoft.com/en-us/azure/cdn/cdn-map-content-to-custom-domain#step-2-create-the-cname-dns-records).

Ha a CNAME rekordot a megfelelő formátumban van, a DigiCert automatikusan ellenőrzi az egyéni tartománynevet, és hozzáadja azt a tulajdonos alternatív neve (SAN) tanúsítvány. DigitCert nem küld egy megerősítési e-mailt, és nem jóvá kell hagynia a kérést. A tanúsítvány egy évre érvényes, és automatikusan-megújításra kerül az Érvényesség lejárata előtt. Folytassa a [3. lépés: Várakozás propagálás](#step-3-wait-for-propagation). 

Az automatikus ellenőrzést általában néhány percre időt vesz igénybe. Ha nem látja a tartomány egy órán belül érvényesítve, a támogatási jegy megnyitása.

#### <a name="cname-record-is-not-mapped-to-cdn-endpoint"></a>CNAME rekordot a rendszer nem rendeli hozzá a CDN-végpont

Ha a végponthoz a CNAME rekord tétel már nem létezik, vagy a cdnverify altartomány tartalmaz, kövesse az utasításokat ebben a lépésben.

HTTPS engedélyezi az egyéni tartomány, miután a DigiCert hitelesítésszolgáltatót (CA) érvényesíti tulajdonjogát, a tartomány kapcsolatba lépve a bejegyzés a tartomány megfelelően [WHOIS](http://whois.domaintools.com/) bejegyzés információkat. Forduljon az e-mail cím keresztül történik (alapértelmezés), vagy a telefonszámot, a WHOIS regisztrációs szerepel. Tartomány ellenőrzéséhez kell végeznie, mielőtt a saját egyéni tartománynévre HTTPS aktív lesz. Hat nap jóváhagyása a tartományban van. Jóvá nem hagyott hat munkanapon belül kérelmek automatikusan törölve lesznek. 

![WHOIS record](./media/cdn-custom-ssl/whois-record.png)

DigiCert ellenőrző e-mailt is küld további e-mail címet. Személyes WHOIS bejegyzés információk esetén ellenőrizze, hogy jóváhagyhatja-ről valamelyik, a következő címre:

Admin @&lt;a tartomány name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
@ gazdáját&lt;a tartomány name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
@ postamester&lt;a tartomány name.com&gt;  

Kap egy e-mailt néhány perc múlva, az alábbi példához hasonló kéri, hogy hagyja jóvá a kérést. Ha levélszemétszűrőt használ, adja hozzá admin@digicert.com számára az engedélyezési lista. Ha 24 órán belül nem kap egy e-mailt, a Microsoft támogatási szolgálatához.
    
![Tartomány-e-mailek ellenőrzése](./media/cdn-custom-ssl/domain-validation-email.png)

Amikor a jóváhagyási hivatkozásra kattint, a rendszer átirányítja a következő online-hoz jóváhagyási űrlapon: 
    
![Tartomány ellenőrzés képernyő](./media/cdn-custom-ssl/domain-validation-form.png)

Kövesse az utasításokat az űrlapon; ellenőrzési két lehetőség közül választhat:

- Ugyanazt a fiókot az azonos gyökértartományban; keresztül az összes jövőbeni megrendelések jóváhagyhatja például: contoso.com. Ezt a módszert akkor ajánlott, ha azt tervezi, hogy további egyéni tartományokat ugyanazon gyökértartományának hozzá.

- Csak a megadott állomás nevét, amelyet a kérés jóváhagyása További jóváhagyási később szükség.

A jóváhagyást követően DigiCert hozzáadja az egyéni tartománynevet a SAN-tanúsítvány. A tanúsítvány egy évre érvényes, és automatikus-megújításra kerül lejárta előtt.

### <a name="step-3-wait-for-propagation"></a>3. lépés: Várakozás terjesztése

A tartománynév érvényesítését követően akár az egyéni tartomány HTTPS szolgáltatás aktiválása 6 – 8 órát is igénybe vehet. Ha a folyamat befejeződik, az egyéni HTTPS az Azure portálon beállítás **engedélyezve** vannak, és az egyéni tartomány párbeszédpanel négy művelet lépéseit el azokat. Az egyéni tartomány már készen áll a HTTPS PROTOKOLLT használja.

![Engedélyezze a HTTPS párbeszédpanel](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>A művelet folyamatban van

A következő táblázat, amely akkor fordul elő, ha engedélyezi a HTTPS művelet végrehajtási állapotát tartalmazza. Miután engedélyezte a HTTPS, négy működtetéshez szükséges lépéseken az egyéni tartomány párbeszédpanel jelenik meg. Minden lépés aktívvá válik, mivel további be az adatok feladatütemezésekben megjelennek, végrehajtás során. Nem minden ezek részlépések történik. Ha egy lépés sikeresen befejeződött, egy zöld pipa jelenik meg látható. 

| A művelet lépés | A művelet be részletei | 
| --- | --- |
| 1 Submitting kérelem | Kérés elküldése |
| | Folyamatban van a HTTPS-kérés elküldése. |
| | Sikerült elküldeni a HTTPS-kérést. |
| 2 tartomány ellenőrzéséhez | Tartomány automatikusan érvényesítve, ha a CDN-végpont leképezve CNAME. Ellenkező esetben a ellenőrzési kérés kapnak az e-mailt a tartomány regisztrációs rekordot (WHOIS bejegyzés) szerepel. Amint lehetséges ellenőrizze a tartomány. |
| | Sikerült ellenőrizni a tartomány tulajdonjogát. |
| | Tartomány tulajdonjoga ellenőrzési kérelem lejárt (ügyfél valószínűleg nem válaszolt 6 napon belül). HTTPS-tartományra nem lesz engedélyezve. * |
| | Tartomány tulajdonjoga ellenőrzési kérelem vissza lett utasítva, az ügyfél által. HTTPS-tartományra nem lesz engedélyezve. * |
| 3 Tanúsítványtelepítés | A hitelesítésszolgáltató jelenleg azon tanúsítvány kibocsátását végzi, amely a HTTPS tartományban való engedélyezéséhez szükséges. |
| | A tanúsítvány már ki van állítva, és telepítése folyamatban van a CDN-hálózathoz. Ez akár 6 órába is telhet. |
| | Sikerült üzembe helyezni a tanúsítványt a CDN hálózatban. |
| 4 kész | Sikerült engedélyezni a HTTPS-t a tartományban. |

\* Ez az üzenet nem jelenik meg, kivéve, ha hiba történt. 


Ha a hiba akkor fordul elő, a küldés előtt, a következő hibaüzenet jelenik meg:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>HTTPS letiltása

Miután engedélyezte az egyéni tartománynév a HTTPS, később letilthatja. Tiltsa le a HTTPS, kövesse az alábbi lépéseket:

### <a name="step-1-disable-the-feature"></a>1. lépés: A funkció letiltásához 

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure CDN Standard verizon** vagy **verizon Azure CDN Premium** CDN-profilt.

2. A végpontok, kattintson az egyéni tartomány tartalmazó végpont.

3. Kattintson az egyéni tartományt, amelynek le szeretné tiltani a HTTPS.

    ![Egyéni tartományok listája](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Kattintson a **ki** HTTPS letiltásához kattintson a **alkalmaz**.

    ![Egyéni HTTPS párbeszédpanel](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>2. lépés: Várakozás terjesztése

Után az egyéni tartomány HTTPS funkció le van tiltva, amíg érvénybe lépéséhez akár 6 – 8 órát is igénybe vehet. Ha a folyamat befejeződik, az egyéni HTTPS az Azure portálon beállítás **letiltott** vannak, és a három működtetéshez szükséges lépéseken az egyéni tartomány párbeszédpanelen el azokat. Az egyéni tartomány már nem használható a HTTPS.

![Tiltsa le a HTTPS párbeszédpanel](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>A művelet folyamatban van

A következő táblázat, amely akkor fordul elő, ha letiltja az HTTPS művelet végrehajtási állapotát tartalmazza. HTTPS letiltását követően három működtetéshez szükséges lépéseken az egyéni tartomány párbeszédpanel jelenik meg. Az egyes lépések válik aktívvá, további részleteket a lépésben jelennek meg. Ha egy lépés sikeresen befejeződött, egy zöld pipa jelenik meg látható. 

| A művelet folyamatban van | Művelet részletei | 
| --- | --- |
| 1 Submitting kérelem | Folyamatban van a kérelem elküldése. |
| 2 tanúsítvány megszüntetés | Tanúsítvány törlése folyamatban |
| 3 kész | Tanúsítvány törölve |

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. *A tanúsítványszolgáltató és használt tanúsítvány típusa?*

    Microsoft DigiCert által biztosított tulajdonos alternatív neve (SAN) tanúsítványt használ. SAN-tanúsítványnak biztonságossá teheti az egy tanúsítvánnyal rendelkező több teljesen minősített tartománynév.

2. *A dedikált tanúsítvány is használható?*
    
    Jelenleg nem de a programba.

3. *Mi történik, ha a tartomány megerősítési e-mailt nem jelenik meg a DigiCert?*

    Ha egy CNAME-bejegyzést az egyéni tartomány, amely közvetlenül a végpont állomásnevéhez mutat (és nem használja a cdnverify altartománynév), nem tartományi ellenőrző e-mailt kap. Érvényesítési automatikusan megtörténik. Ha nem rendelkezik egy CNAME-bejegyzést, és 24 órán belül nem kapott e-mailt, ellenkező esetben forduljon a Microsoft támogatási szolgálatához.

4. *Kevésbé biztonságos, mint egy dedikált tanúsítvány SAN tanúsítványt használ?*
    
    SAN-tanúsítványnak a következő ugyanolyan titkosítási és biztonsági szinten dedikált tanúsítványként. Összes kiállított SSL-tanúsítvány a fokozott biztonság SHA-256 használatát.

5. *Használható-e az egyéni tartománynév HTTPS Akamai Azure CDN?*

    Ez a funkció jelenleg csak Azure CDN Verizon érhető el. Ez a szolgáltatás Akamai Azure CDN az elkövetkező hónapokban támogató Microsoft dolgozik.

6. *Kell egy tanúsítványt hatóság engedélyezési bejegyzést a DNS-szolgáltatónál?*

    Nem, nincs jelenleg szükséges egy tanúsítvány hatóság engedélyezési bejegyzést. Azonban ha nincs fiókja, magában kell foglalnia DigiCert érvényes hitelesítésszolgáltatóként.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan állíthat be egy [Azure CDN-végpont egyéni tartományhoz](./cdn-map-content-to-custom-domain.md)


