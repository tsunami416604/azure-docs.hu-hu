---
title: "Személyes adatok védelmére átvitel a titkosítás az Azure-ban |} Microsoft Docs"
description: "Azure titkosítással személyes adatok védelme"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 461ddfda796bfe6639e27f6c4cd53e82b4e397d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-in-transit-with-encryption"></a>Az Azure titkosítási technológiák: személyes adatok védelmére átvitel titkosítással

Ez a cikk segít megértéséhez, valamint a védett adatok Azure titkosítási technológiák használata az átvitel során. 

Fontos része a többrétegű védelemmel az olyan jellegű biztonsági stratégia védelme a személyes adatokat a hálózaton keresztül. Titkosítási átvitel célja egy támadó elfogja az átvitelt nem tudja megtekinteni, illetve az adatok felhasználásával megelőzése érdekében.

## <a name="scenario"></a>Forgatókönyv

Egy nagy körutazás cég, az Amerikai Egyesült Államokban telephelyének bővíti a műveleteket a mediterrán, Adriai, és Balti tengerek, valamint a Brit-szigetekre útvonalak biztosítani. Támogatás ezeket, több kisebb körutazás sorok Olaszország, Németországban, Dánia és az Egyesült szerzett 

A vállalat a Microsoft Azure használ a felhőben tárolt vállalati adatokat. Ez magában foglalja a személyes azonosításra alkalmas adatokat, például neveket, címeket, telefonszámokat, és a globális felhasználói bázis hitelkártya adatait. Az összes hely például a címet, telefonszámot, azonosító számokat és egyéb információkat a vállalat alkalmazottai hagyományos emberi erőforrás adatokat is tartalmaz. A körutazás sor is fenntartja, nagy adatbázis ellenszolgáltatás és hűség program tagok nyomon követéséhez a kapcsolatokat az aktuális és korábbi ügyfelek személyes információkat is tartalmaz.

Személyes ügyfelek adatok az adatbázis távoli iroda a vállalat és a világ utazás ügynökök. Felhasználói adatokat tartalmazó dokumentumok továbbítja a hálózaton az Azure Storage.

## <a name="problem-statement"></a>Probléma leírása

A vállalat kell az ügyfelek és az alkalmazottak személyes adatok védelme, amikor az Azure-szolgáltatásokhoz érkező vagy oda irányuló útközben.

## <a name="company-goal"></a>Vállalati cél

A vállalat célja, hogy a személyes adatok lemez ki legyen titkosítva. Jogosulatlan személyek intercept a lemez személyes adatokat, ha megjelenítéséhez, nem olvasható formátumban kell lennie. Titkosítás alkalmazásakor kell lennie, vagy az teljesen átlátható, felhasználók és rendszergazdák könnyen.

## <a name="solutions"></a>Megoldások

Azure-szolgáltatások és több segítséget a személyes adatok védelmére átvitel technológiák biztosítása.

### <a name="azure-storage"></a>Azure Storage

A felhőben tárolt adatokat az Azure-adatközpontot az ügyfélről, amely fizikailag bárhol a világon, kell keresnie. Amikor a felhasználók lekéri az adatokat, akkor újra, az ellenkező irányba választ. Adatok az átvitel során a nyilvános interneten keresztül mindig az támadók hozzáférés veszélyben van. Fontos az személyes adatok védelmét átviteli szintű titkosítást biztonságossá tételéhez, helyek közötti átvitel során.

A HTTPS protokoll egy biztonságos, titkosított kommunikációs csatornát biztosít az interneten keresztül. HTTPS használatával érhető el az Azure Storage és objektumok, amikor a REST API-k hívása. Ha kényszeríti a HTTPS protokoll használatát [megosztott hozzáférési aláírásokkal](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) (SAS) delegálása az Azure Storage-objektumokhoz való hozzáférést. SAS két típusa van: szolgáltatás SAS és a fiók SAS.

#### <a name="how-do-i-construct-a-service-sas"></a>Hogyan hozható létre a szolgáltatás SAS?

A szolgáltatás SAS delegálja a tároló szolgáltatást (blob, a várólista, a tábla vagy a fájl szolgáltatás) csak egy erőforráshoz való hozzáférés. A szolgáltatás SAS összeállításához, tegye a következőket:

1. Adja meg az aláírt verzió mező

2. Adja meg az aláírt erőforrás (Blob és csak a File szolgáltatás)

3. Válaszfejlécek (Blob és csak a fájl szolgáltatás) felülbírálása a lekérdezési paraméterek megadása

4. Adja meg a táblanevet (csak a Table szolgáltatás)

5. Adja meg a hozzáférési házirend

6. Adja meg az aláírás érvényességi időszakának

8. Engedélyek megadása

9. Adja meg az IP-cím vagy IP-címtartomány

10. Adja meg a HTTP protokoll

11. Adja meg a hozzáférés tartomány

12. Adja meg az aláírt azonosítója

13. Adja meg az aláírás

Részletes utasítások, lásd: [hozhat létre, egy szolgáltatás SAS](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS?redirectedfrom=MSDN).

#### <a name="how-do-i-construct-an-account-sas"></a>Hogyan hozható létre egy fiókot SAS?

Egy fiók SAS egy vagy több, a tárolási szolgáltatások erőforrásokhoz való hozzáférést biztosít. A blobtárolók, táblák, üzenetsorok és fájlmegosztások olvasási, írási és törlési műveleteihez is hozzáférést biztosíthat, amelyeket a szolgáltatásalapú SAS nem engedélyez. Egy fiók SAS építése hasonlít a szolgáltatás SAS. Részletes útmutatásért lásd: [hozhat létre egy fiókot SAS.](https://docs.microsoft.com/rest/api/storageservices/Constructing-an-Account-SAS?redirectedfrom=MSDN)

#### <a name="how-do-i-enforce-https-when-calling-rest-apis"></a>Hogyan kényszerítése meg HTTPS, amikor a program meghívja a REST API-k?

A HTTPS protokoll használatát a storage-fiókok hozzáférést objektumokhoz REST API-k meghívásakor kényszerítéséhez engedélyezheti biztonságos átviteléhez szükséges a tárfiók. 

1. Válassza ki az Azure-portálon **Storage-fiók létrehozása**, vagy meglévő tárfiókot, válassza a **beállítások** , majd **konfigurációs**.

2. A **biztonságos átviteléhez szükséges**, jelölje be **engedélyezve**.

![A storage-fiók létrehozása](media/protect-personal-data-in-transit-encryption/create-storage-account.png)

Részletesebb leírását, beleértve a programozott módon engedélyezze a biztonságos átviteléhez szükséges tudnivalókat [szükséges biztonságos átviteli](https://docs.microsoft.com/azure/storage/storage-require-secure-transfer).

#### <a name="how-do-i-encrypt-data-in-azure-file-storage"></a>Hogyan titkosítják az adatokat az Azure File Storage?

Az átvitt adatokat titkosítani [Azure File Storage](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-portal), használhatja az SMB Windows 8, 8.1 és 10 és Windows Server 2012 R2 és Windows Server 2016 3.x. Az Azure Fileshoz szolgáltatást használja, bármilyen titkosítás nélküli kapcsolatot sikertelen lesz, ha engedélyezve van a "Biztonságos szükséges átviteli". Ez magában foglalja az SMB 2.1, az SMB 3.0-titkosítás nélkül és az egyes változatban is elkészíti a Linux SMB-ügyfél a forgatókönyveket.

#### <a name="azure-client-side-encryption"></a>Az Azure ügyféloldali titkosítás

Másik lehetőségként a személyes adatok védelme alatt álló ügyfélalkalmazást és az Azure Storage közötti átvitel közben [ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/storage-client-side-encryption). Az Azure Storage átvitele előtt titkosítja az adatokat, és amikor az adatok Azure Storage-ból, az adatok visszafejtése ügyféloldali fogadását követően.

### <a name="azure-site-to-site-vpn"></a>Az Azure--webhelyek közötti VPN

Hatékony módot személyes adatok védelmére átvitel a vállalati hálózathoz vagy a felhasználó és az Azure virtuális hálózat között, hogy használja a [pont-pont](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) vagy [pont-pont](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) virtuális magánhálózati (VPN). VPN-kapcsolatot hoz létre a titkosított biztonságos alagutat az interneten keresztül.

#### <a name="how-do-i-create-a-site-to-site-vpn-connection"></a>Hogyan hozható létre a pont-pont VPN-kapcsolatot?

A telephelyek közötti VPN a vállalati hálózaton lévő több felhasználó csatlakozik Azure. Pont-pont kapcsolat létrehozása az Azure portálon, tegye a következőket:

1. Hozzon létre egy virtuális hálózatot.

2. Adjon meg egy DNS-kiszolgáló.

3. Hozza létre az átjáró-alhálózatot.

4. Hozza létre a VPN-átjárót. 

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-01.png)

5. A helyi hálózati átjáró létrehozása.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-02.png)

6. Konfigurálja a VPN-eszköz.

7. A VPN-kapcsolat létrehozása.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-03.png)

8. A VPN-kapcsolat ellenőrzése.

A helyek kapcsolat létrehozása az Azure portálon részletes utasításokért lásd: [hozzon létre egy webhelyek kapcsolatot az Azure portálon.] (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

#### <a name="how-do-i-create-a-point-to-site-vpn-connection"></a>Hogyan hozható létre egy pont – hely VPN-kapcsolatot?

A pont-pont VPN biztonságos kapcsolatot hoz létre egyéni ügyfél-számítógépről egy virtuális hálózathoz. Ez akkor hasznos, ha, amelyhez csatlakozni az Azure-bA egy távoli helyről, például otthoni vagy a szállodák vagy konferencia központból. Pont – hely kapcsolat létrehozása az Azure-portálon

1. Hozzon létre egy virtuális hálózatot.

2. Adjon hozzá egy átjáró-alhálózatot.

3. Adjon meg egy DNS-kiszolgáló. (nem kötelező)

4. A virtuális hálózati átjáró létrehozása.

5. Tanúsítványok előállításához.

6. Adja hozzá az ügyfél-címkészlet.

7. Töltse fel a legfelső szintű tanúsítványának nyilvános tanúsítvány adatait.

8. Létrehozni, és a VPN-ügyfélcsomag konfigurációs telepítse.

9. Telepíti az exportált ügyféltanúsítványt.

10. Csatlakozás az Azure-bA.

11. Ellenőrizze a kapcsolatot.

Részletes utasítások, lásd: [egy Vnetet tanúsítvány hitelesítése a pont-pont kapcsolat: Azure-portálon.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)

### <a name="ssltls"></a>SSL/TLS

A Microsoft azt javasolja, hogy mindig SSL/TLS protokollokat használ az exchange-adatok különböző helyek között. A szervezetek, amelyek [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) nagy adatkészletek áthelyezése egy dedikált nagy sebességű WAN-KAPCSOLATON keresztül hivatkozásra is az adatokat titkosíthatja az alkalmazás szintjén SSL/TLS-vagy egyéb protokollok, a hozzá lett adva.

### <a name="encryption-by-default"></a>Alapértelmezés szerint titkosítás

Microsoft-titkosítást használ az adatok védelmére átvitel ügyfelek és az Azure felhőszolgáltatások között. Az Azure Storage az Azure-portálon való interakció, minden tranzakció történik meg HTTPS használatával.

[Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) a protokoll, amelyet a Microsoft azon adatközpontjainak megkísérli egyeztetni az ügyfélszámítógépeken, a Microsoft felhőalapú szolgáltatásokhoz. A TLS nyújt erős hitelesítés, üzenetvédelem, és integritását (lehetővé teszi, hogy a üzenet illetéktelen hozzáférés és hamisítására észlelése), együttműködés, az algoritmusok rugalmassága, könnyű telepítése és használata.

[Továbbítási titkosítása tökéletes](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) is, hogy az egyes ügyfelek ügyfél és a Microsoft olyan felhőszolgáltatásai közötti kapcsolat egyedi kulcsok használata alkalmazzák. Kapcsolatok a Microsoft felhőszolgáltatásai kihasználhatják alapú RSA 2048 bites titkosítást kulcshossza. A TLS, RSA 2048 bit, kulcshosszok kombinációja, így a PFS jelentős mértékben megnehezíti mások intercept és a hozzáférési adatok, amely a Microsoft más felhőszolgáltatásaival és az ügyfelek közötti átvitel közben.

Adatok az átvitel során mindig titkosítja az [Data Lake Store] (https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview). Amellett, hogy az adatok titkosítása az állandó adathordozón való tárolás előtt történik meg, az átvitt adatok is mindig titkosítva vannak HTTPS segítségével. A HTTPS az egyetlen olyan protokoll, amely támogatott a Data Lake Store REST-felületeihez.

## <a name="summary"></a>Összefoglalás

A vállalat személyes adatok és az ilyen adatok védelmének azáltal, Azure Storage HTTPS-kapcsolatok, megosztott hozzáférési aláírásokkal használatával biztonságos átviteléhez szükséges engedélyezését a storage-fiókok a cél megvalósításához. Személyes adatok védelme az SMB 3.0-kapcsolatot is használ, és ügyféloldali titkosítás végrehajtásával is. Pont-pont VPN-kapcsolatok az Azure virtuális hálózat számára a vállalati hálózatról és a pont-pont VPN-kapcsolatok az egyes felhasználók létrehoz egy biztonságos csatornán keresztül, amelyek személyes adatokat is biztonságosan változatlan marad. A Microsoft alapértelmezett titkosítási eljárásokat további megvédi az személyes adatok védelme.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Data biztonsági és a titkosítás gyakorlati tanácsok](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices)

- [A VPN Gateway tervezése és kialakítása](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

- [VPN Gateway – gyakori kérdések](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq)

- [Vásároljon és SSL-tanúsítvány konfigurálása az Azure App Service](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)
