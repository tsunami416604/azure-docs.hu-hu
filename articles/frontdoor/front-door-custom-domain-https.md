---
title: Oktatóanyag – HTTPS konfigurálása egyéni tartományon az Azure Front Door Service-ben | Microsoft Docs
description: Ebben az oktatóanyagban megismerheti, hogyan engedélyezheti és tilthatja le a HTTPS-t egy egyéni tartomány Azure Front Door Service-konfigurációjában.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2018
ms.author: sharadag
ms.openlocfilehash: 3df96451838fe90b7d45d1aedd272fc10d798e57
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883975"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Oktatóanyag: HTTPS konfigurálása Front Door egyéni tartományon

Ez az oktatóanyag bemutatja, hogyan lehet engedélyezni a HTTPS-protokollt az előtérbeli gazdagépek szakaszban egy Front Doorhoz kapcsolódó egyéni tartomány esetében. A HTTPS-protokoll egyéni tartományon belüli használatával (például https:\//www.contoso.com) biztosítható, hogy a bizalmas adatokat a rendszer biztonságosan, TLS/SSL-titkosításon keresztül továbbítsa az Interneten. Amikor a böngésző HTTPS-protokollal kapcsolódik egy webhelyhez, ellenőrzi a webhely biztonsági tanúsítványát, és megállapítja, hogy azt arra jogosult hitelesítésszolgáltató adta-e ki. Ez az eljárás védelmet nyújt webalkalmazásai számára a támadásokkal szemben.

Az Azure Front Door Service alapértelmezés szerint támogatja a HTTPS-t az alapértelmezett Front Door-gazdagépnév esetében. Ha például Front Doort hoz létre (pl. https:\//contoso.azurefd.net), a HTTPS automatikusan engedélyezve lesz a https://contoso.azurefd.net címre küldött kérések esetében. Azonban a „www.contoso.com” egyéni tartomány regisztrálása után külön engedélyeznie kell a HTTPS-t az előtérbeli gazdagépen.   

Az egyéni HTTPS szolgáltatás legfőbb jellemzői a következők:

- Nincsenek további költségek: a tanúsítvány megszerzése vagy megújítása ingyenes, és a HTTPS-forgalom sem von maga után további költségeket. 

- Egyszerű engedélyezés: a kiépítés egy kattintással elvégezhető az [Azure Portalon](https://portal.azure.com) keresztül. A szolgáltatás engedélyezéséhez REST API-k, valamint más fejlesztői eszközök is használhatók.

- Teljes körű tanúsítványkezelés érhető el: nem kell foglalkoznia a tanúsítványok beszerzésével és kezelésével. A tanúsítványok üzembe helyezése és megújítása automatikusan megtörténik a lejárat előtt, így nem kell attól tartani, hogy a szolgáltatás megszakad egy lejárt tanúsítvány miatt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - HTTPS-protokoll engedélyezése az egyéni tartományon
> - AFD által kezelt tanúsítvány használata 
> - Saját tanúsítvány használata, amely egyéni SSL-tanúsítvány
> - A tartomány érvényesítése
> - HTTPS-protokoll letiltása az egyéni tartományon

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy legalább egy egyéni tartománnyal regisztrált Front Doort. További információkért lásd: [Útmutató: Egyéni tartomány hozzáadása a Front Doorhoz](front-door-custom-domain.md)

## <a name="ssl-certificates"></a>SSL-tanúsítványok

Ha HTTPS-protokollt szeretne engedélyezni egy egyéni Front Door-tartomány tartalmának biztonságos továbbításához, SSL-tanúsítványt kell használnia. Az Azure Front Door Service által kezelt vagy saját tanúsítványt használhat.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>1. lehetőség (alapértelmezett): A Front Door által felügyelt tanúsítvány használata

Ha az Azure Front Door Service által kezelt tanúsítványt használ, a HTTPS szolgáltatás mindössze néhány kattintással bekapcsolható. Az Azure Front Door Service elvégzi az összes tanúsítványkezelési feladatot, például a beszerzést és a megújítást. A szolgáltatás engedélyezése után a folyamat azonnal elindul. Ha az egyéni tartomány már hozzá van rendelve a Front Door alapértelmezett előtérbeli gazdagépéhez (`{hostname}.azurefd.net`), nincs további teendő. A Front Door automatikusan feldolgozza a lépéseket és végrehajtja a kérést. Ha azonban az egyéni tartomány más helyre van leképezve, meg kell erősítenie a tartomány tulajdonjogát e-mailben.

Kövesse az alábbi lépéseket a HTTPS engedélyezéséhez egy egyéni tartományon:

1. Az [Azure Portalon](https://portal.azure.com) keresse meg **Front Door**-profilját.

2. Az előtérbeli gazdagépek listájából válassza ki azt az egyéni tartományt, amelyen engedélyezni szeretné a HTTPS-t az egyéni tartomány tárolásához.

3. Az **egyéni tartományi HTTPS** szakaszban kattintson az **Engedélyezve** elemre, és válassza ki a **Front Door által felügyelt** tanúsítványforrást.

4. Kattintson a Mentés gombra.

5. Folytassa [A tartomány érvényesítése](#validate-the-domain) című szakasszal.


### <a name="option-2-use-your-own-certificate"></a>2. lehetőség: Saját tanúsítvány használata

A saját tanúsítványát is használhatja a HTTPS szolgáltatás engedélyezéséhez. Ez a folyamat Azure Key Vault-integrációval történik, amely lehetővé teszi a tanúsítványok biztonságos tárolását. Az Azure Front Door Service ezt a biztonságos mechanizmust használja a tanúsítvány beszerzéséhez, és néhány további lépést igényel. SSL-tanúsítványt egy engedélyezett hitelesítésszolgáltatóval (CA) kell létrehoznia. Másként, nem engedélyezett CA használata igénybe vétele esetén a kérelme vissza lesz utasítva. Az engedélyezett CA-k megtalálhatók az [egyéni HTTPS az Azure Front Door Service-ben való engedélyezéséhez jóváhagyott hitelesítésszolgáltatók listájában](front-door-troubleshoot-allowed-ca.md).

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Az Azure Key Vault-fiók és a tanúsítvány előkészítése
 
1. Azure Key Vault: Rendelkeznie kell egy futó Azure Key Vault-fiókkal abban az előfizetésben, amelyben az a Front Door található, amelyhez egyéni HTTPS-t szeretne engedélyezni. Ha még nem rendelkezik Azure Key Vault-fiókkal, hozzon létre egyet.
 
2. Azure Key Vault-tanúsítványok: Ha már rendelkezik tanúsítvánnyal, feltöltheti közvetlenül az Azure Key Vault-fiókjába, vagy létrehozhat egy új tanúsítványt közvetlenül az Azure Key Vaultban azokkal a hitelesítésszolgáltató (CA) partnerekkel, amelyekkel az Azure Key Vault integrálva van.

> [!WARNING]
> </br> – Az Azure Front Door Service jelenleg csak olyan Key Vault-fiókokat támogat, amelyek a Front Door-konfigurációval megegyező előfizetésben vannak. Ha nem a Front Door-előfizetéshez tartozó Key Vault-tárolót választ, az hibát eredményez.
> </br> – Az Azure Front Door Service jelenleg csak a Titkos kódok szakaszban tárolt Key Vault-tanúsítványokat támogatja. A tanúsítványt nem fogja tudni importálni, ha a Titkos kódok helyett a Tanúsítványok szakaszban tárolja.
> </br> – Az Azure Front Door Service jelenleg csak a jelszó **nélküli**, PFX-szel feltöltött tanúsítványokat támogatja.

#### <a name="register-azure-front-door-service"></a>Az Azure Front Door Service regisztrálása

Regisztrálja az Azure Front Door Service szolgáltatásnevét alkalmazásként az Azure Active Directoryban a PowerShell használatával.

1. Ha szükséges, telepítse az [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM/6.0.0) bővítményt a PowerShellhez a helyi számítógépen.

2. Futtassa a PowerShellben az alábbi parancsot:

     `New-AzureRmADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-service-access-to-your-key-vault"></a>Hozzáférés biztosítása az Azure Front Door Service számára a Key Vaulthoz
 
Engedélyt kell adnia az Azure Front Door Service számára, hogy hozzáférhessen az Azure Key Vault-fiók Titkos kódok szakaszában tárolt tanúsítványokhoz.

1. A Key Vault-fiók BEÁLLÍTÁSOK területén válassza a **Hozzáférési szabályzatok**, majd az **Új hozzáadása** lehetőséget új szabályzat létrehozásához.

2. A **Rendszerbiztonsági tag kijelölése** területen keresse meg az **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037** elemet, és válassza ki a **Microsoft.Azure.Frontdoor** lehetőséget. Kattintson a **Kiválasztás** gombra.


3. A **Titkos kód engedélyei** területen kattintson a **Lekérdezés** elemre, hogy engedélyezze a Front Door számára az engedélyek elvégzését a tanúsítványok listájának lekérdezéséhez. 

4. Kattintson az **OK** gombra. 

    Az Azure Front Door Service most már hozzáférhet a Key Vaulthoz és az abban tárolt tanúsítványokhoz (titkos kódokhoz).
 
#### <a name="select-the-certificate-for-azure-front-door-service-to-deploy"></a>Az Azure Front Door Service által üzembe helyezendő tanúsítvány kiválasztása
 
1. Lépjen vissza a Front Doorhoz a portálon. 

2. Az egyéni tartományok listájából válassza ki azt az egyéni tartományt, amelyen engedélyezni szeretné a HTTPS-t.

    Megjelenik az **Egyéni tartomány** lap.

3. A Tanúsítványkezelés típusa területen válassza a **Saját tanúsítvány használata** lehetőséget. 

4. Az Azure Front Door Service használatához a Key Vault-fiók előfizetésének meg kell egyeznie a Front Door előfizetésével. Válassza ki a Key Vaultot, a tanúsítványt (titkos kódot) és a tanúsítványverziót.

    Az Azure Front Door Service a következő információkat jeleníti meg: 
    - Az előfizetés azonosítójához tartozó Key Vault-fiókok. 
    - A kiválasztott Key Vaultban található tanúsítványok (titkos kódok). 
    - A tanúsítvány elérhető verziói. 
 
5. Saját tanúsítvány használatakor nem szükséges tartományérvényesítés. Lépjen tovább a [Várakozás a propagálásra](#wait-for-propagation) részhez.

## <a name="validate-the-domain"></a>A tartomány érvényesítése

Ha már rendelkezik használatban lévő egyéni tartománnyal, amely az egyéni végpontjára van leképezve egy CNAME rekorddal, vagy ha saját tanúsítványt használ, lépjen tovább a következőre:  
[Az egyéni tartomány le van képezve a Front Doorra](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record). Ha már nem létezik a tartomány CNAME rekordjának bejegyzése, vagy az afdverify altartományt tartalmazza, lépjen tovább [Az egyéni tartomány nincs leképezve a Front Doorra](#custom-domain-is-not-mapped-to-your-front-door) című részre.

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>Az egyéni tartomány le van képezve a Front Doorra egy CNAME rekorddal

Amikor hozzáadott egy egyéni tartományt a Front Door előtérbeli gazdagépeihez, létrehozott egy CNAME rekordot a saját tartományregisztrálójának DNS-táblájában, hogy leképezze a Front Door alapértelmezett .azurefd.net eszköznevére. Ha ez a CNAME rekord még létezik és nem tartalmazza az afdverify altartományt, a DigiCert hitelesítésszolgáltató arra használja, hogy automatikusan érvényesítse az egyéni tartomány tulajdonjogát. 

Ha saját tanúsítványt használ, nem szükséges tartományérvényesítés.

A CNAME rekordnak a következő formátumban kell lennie, ahol a *Név* az Ön egyéni tartományának neve, az *Érték* pedig a Front Door alapértelmezett .azurefd.net eszközneve:

| Name (Név)            | Típus  | Érték                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azurefd.net |

A CNAME rekordokkal kapcsolatos további információért tekintse meg a [CNAME DNS-rekord létrehozását ismertető](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain#create-the-cname-dns-records) részt.

Ha a CNAME rekordja a megfelelő formátumban van, a DigiCert automatikusan ellenőrzi az egyéni tartománynevet, és létrehoz egy dedikált tanúsítványt. A DigitCert nem küld visszaigazoló e-mailt, és nem kell jóváhagynia a kérést. A tanúsítvány egy évig érvényes, és a lejárata előtt automatikusan megújul. Lépjen tovább a [Várakozás a propagálásra](#wait-for-propagation) részhez. 

Az automatikus érvényesítés általában eltart néhány percig. Ha a tartománya egy órán belül sincs érvényesítve, nyisson meg egy támogatási jegyet.

>[!NOTE]
>Ha van egy Hitelesítésszolgáltatói engedélyezési (CAA-) rekordja a DNS-szolgáltatónál, tartalmaznia kell a DigiCertet mint érvényes hitelesítésszolgáltatót. A CAA-rekord lehetővé teszi a tartomány tulajdonosai számára, hogy megadják a DNS-szolgáltatóknál, hogy melyik hitelesítésszolgáltatók jogosultak a tartomány tanúsítványának kiállítására. Ha egy hitelesítésszolgáltató kérést kap egy CAA-rekorddal rendelkező tartomány tanúsítványának kiállítására, és a hitelesítésszolgáltató nem szerepel az engedélyezett kiállítók listáján, nem adhat ki tanúsítványt a tartománynak vagy altartománynak. További információ a CAA-rekordok kezelésével kapcsolatban: [CAA-rekordok kezelése](https://support.dnsimple.com/articles/manage-caa-record/). A CAA-rekordokhoz való eszközért lásd: [CAA-rekord segítő](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>Az egyéni tartomány nincs leképezve a Front Doorra

Ha már nem létezik a végpont CNAME rekordjának bejegyzése, vagy az afdverify altartományt tartalmazza, kövesse az itt ismertetett lépéseket.

Miután engedélyezi a HTTPS-t az egyéni tartományhoz, A DigiCert CA érvényesíti a tartomány tulajdonjogát azáltal, hogy kapcsolatba lép a regisztrálójával a tartomány [WHOIS](http://whois.domaintools.com/) regisztrálójának információja alapján. A kapcsolatfelvétel a WHOIS-regisztrációban megadott e-mail-címen (alapértelmezett) vagy telefonszámon keresztül történik. A HTTPS csak a tartomány hitelesítése után aktiválódik az egyéni tartományon. A tartomány jóváhagyására hat munkanap áll rendelkezésére. A hat munkanapon belül jóvá nem hagyott kérelmek automatikusan törlődnek. 

![WHOIS-rekord](./media/front-door-custom-domain-https/whois-record.png)

A DigiCert további e-mail-címekre is küld megerősítő e-mailt. Ha a WHOIS-regisztráló információja bizalmas, erősítse meg, hogy a jóváhagyást el tudja végezni közvetlenül a következő címek egyikéről:

admin@&lt;az-ön-tartományneve.com&gt;  
administrator@&lt;az-ön-tartományneve.com&gt;  
webmaster@&lt;az-ön-tartományneve.com&gt;  
hostmaster@&lt;az-ön-tartományneve.com&gt;  
postmaster@&lt;az-ön-tartományneve.com&gt;  

Pár percen belül a következőhöz hasonló e-mailt kell kapnia, amely a kérés jóváhagyására kéri. Ha spam szűrőt használ, adja hozzá a(z) admin@digicert.com címet az engedélyezési listához. Ha 24 órán belül nem kapja meg az e-mailt, lépjen kapcsolatba a Microsoft támogatási szolgálatával.

Ha a jóváhagyási hivatkozásra kattint, a rendszer átirányítja egy online jóváhagyási űrlapra. Kövesse az űrlap utasításait; két ellenőrzési lehetősége van:

- Az ugyanazon gyökértartományhoz tartozó ugyanazon fiók összes jövőbeli kérést jóváhagyhatja; például: contoso.com. Ez akkor ajánlott, ha további egyéni tartományokat tervez hozzáadni ugyanazon gyökértartományhoz.

- Jóváhagyhatja az adott gazdanevet, amelyet a kéréshez használtak. A további kérésekhez további jóváhagyás szükséges.

A jóváhagyás után a DigiCert befejezi az egyéni tartománynév tanúsítványának létrehozását. A tanúsítvány egy évig érvényes, és a lejárata előtt automatikusan megújul.

## <a name="wait-for-propagation"></a>Várakozás a propagálásra

A tartománynév érvényesítése után 6-8 óra szükséges ahhoz, hogy az egyéni tartományhoz tartozó HTTPS szolgáltatás aktiválódjon. Ha a folyamat befejeződött, az egyéni HTTPS állapota **Engedélyezve** értékre vált az Azure Portalon, és a négy műveleti lépés az egyéni tartomány párbeszédpanelében befejezettnek lesz jelölve. Az egyéni tartomány ezzel készen áll a HTTPS használatára.

### <a name="operation-progress"></a>Műveleti folyamat

Az alábbi táblázat a műveleti folyamatot mutatja, amely a HTTPS engedélyezésekor megy végbe. Miután engedélyezte a HTTPS-t, négy műveleti lépés jelenik meg az egyéni tartomány párbeszédpaneljében. Ahogy az egyes lépések aktívvá válnak, a folyamat előrehaladtával további allépések részletei jelennek meg a lépés alatt. Nem minden allépés fog előfordulni. Miután egy lépés sikeresen befejeződik, egy zöld pipa jelenik meg mellette. 

| Műveleti lépés | Műveleti allépés részletei | 
| --- | --- |
| 1. Kérés elküldése | Kérés elküldése |
| | A HTTPS-kérés küldése folyamatban van. |
| | A HTTPS-kérés elküldése sikerült. |
| 2. Tartományérvényesítés | A tartomány automatikusan érvényesítve lesz, ha a CNAME révén le van képezve a Front Door alapértelmezett .azurefd.net előtérbeli gazdagépéhez. Ha nincs, akkor visszaigazolási kérelem érkezik a tartomány regisztrációs rekordjában megadott e-mail-címre (WHOIS regisztráló). Minél hamarabb igazolja vissza a tartományt. |
| | Sikerült ellenőrizni a tartomány tulajdonjogát. |
| | A tartomány tulajdonjogának ellenőrzési kérelme lejárt (az ügyfél valószínűleg nem válaszolt 6 napon belül). A HTTPS nem lesz engedélyezve a tartományon. * |
| | A tartomány tulajdonjogának ellenőrzésére vonatkozó kérelem vissza lett utasítva az ügyfél által. A HTTPS nem lesz engedélyezve a tartományon. * |
| 3. Tanúsítvány üzembe helyezése | A hitelesítésszolgáltató jelenleg azon tanúsítvány kibocsátását végzi, amely a HTTPS tartományban való engedélyezéséhez szükséges. |
| | A tanúsítvány kibocsátása megtörtént, és a Front Doorban való üzembe helyezése folyamatban van. A folyamat akár 1 órát is igénybe vehet. |
| | A tanúsítvány sikeresen üzembe lett helyezve a Front Doorhoz. |
| 4. Befejezve | Sikerült engedélyezni a HTTPS-t a tartományban. |

\* Ez az üzenet csak akkor jelenik meg, ha hiba történt. 

Ha a kérelem elküldése előtt hiba történik, a következő hibaüzenet jelenik meg:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Az erőforrások eltávolítása – HTTPS letiltása

Az előző lépések során engedélyezte a HTTPS protokollt az egyéni tartományon. Ha már nem szeretné HTTPS-sel használni az egyéni tartományt, letilthatja a HTTPS-t a következő lépések végrehajtásával:

### <a name="disable-the-https-feature"></a>HTTPS szolgáltatás letiltása 

1. Az [Azure Portalon](https://portal.azure.com) keresse meg az **Azure Front Door Service** konfigurációját.

2. Az előtérbeli gazdagépek listájában válassza ki azt az egyéni tartományt, amelyen le szeretné tiltani a HTTPS-t.

3. A HTTPS letiltásához kattintson a **Letiltva**, majd a **Mentés** gombra.

### <a name="wait-for-propagation"></a>Várakozás a propagálásra

Az egyéni tartomány HTTPS szolgáltatásának letiltása után 6-8 óra szükséges ahhoz, hogy a művelet végbemenjen. Ha a folyamat befejeződött, az egyéni HTTPS állapota **Letiltva** értékre vált az Azure Portalon, és a három műveleti lépés az egyéni tartomány párbeszédpanelében befejezettnek lesz jelölve. Az egyéni tartomány már nem használhatja a HTTPS-t.

#### <a name="operation-progress"></a>Műveleti folyamat

Az alábbi táblázat a műveleti folyamatot mutatja, amely a HTTPS letiltásakor megy végbe. Miután letiltotta a HTTPS-t, három műveleti lépés jelenik meg az egyéni tartomány párbeszédpaneljében. Ahogy az egyes lépések aktívvá válnak, további részletek jelennek meg a lépés alatt. Miután egy lépés sikeresen befejeződik, egy zöld pipa jelenik meg mellette. 

| Műveleti folyamat | Művelet részletei | 
| --- | --- |
| 1. Kérés elküldése | A kérelem elküldése folyamatban van |
| 2. Tanúsítvány megszüntetése | Tanúsítvány törlése |
| 3. Befejezve | Tanúsítvány törölve |

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. *Ki a tanúsítványszolgáltató és milyen típusú tanúsítvány van használatban?*

    A rendszer egy Digicert által biztosított dedikált/egyetlen tanúsítványt használ az egyéni tartományhoz. 

2. *IP-címalapú vagy SNI TLS/SSL-t használ?*

    Az Azure Front Door Service SNI TLS/SSL-t használ.

3. *Mi a teendő, ha nem kapok visszaigazolási e-mailt a DigiCerttől?*

    Ha van olyan CNAME-bejegyzése az egyéni tartomány esetében, amely közvetlenül a gazdanév végpontjára mutat (és nem használja az afdverify altartománynevet), nem fog a tartomány visszaigazolására vonatkozó e-mailt kapni. A hitelesítés automatikusan történik. Máskülönben, ha nem rendelkezik CNAME-bejegyzéssel, és 24 órán belül nem kapott e-mailt, forduljon a Microsoft támogatási szolgálatához.

4. *A SAN tanúsítvány használata kevésbé biztonságos, mint egy dedikált tanúsítvány használata?*
    
    A SAN-tanúsítvány ugyanolyan titkosítási és biztonsági előírásokat követ, mint a dedikált tanúsítvány. Az összes kiállított SSL-tanúsítvány az SHA-256-ot használja a kiszolgáló fokozott biztonsága érdekében.

5. *Szükségem van hitelesítésszolgáltató engedélyezési rekordra a DNS szolgáltatómnál?*

    Nem, hitelesítésszolgáltatói engedélyezési rekordra jelenleg nincs szükség. Viszont ha van ilyenje, mindenképpen tartalmaznia kell a DigiCertet mint érvényes CA-t.


## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.