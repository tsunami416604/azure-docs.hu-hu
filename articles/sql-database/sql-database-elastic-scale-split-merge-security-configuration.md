---
title: Biztonság szétválasztás és egyesítés konfigurációs |} A Microsoft Docs
description: Állítsa be a x409 titkosítási tanúsítványok a felosztás/egyesítés szolgáltatás rugalmas skálázásra tervezve.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: de758d38626107f28211f79a4772c3e887085776
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599832"
---
# <a name="split-merge-security-configuration"></a>Biztonság szétválasztás és egyesítés konfiguráció
A felosztás/egyesítés szolgáltatás használatához a megfelelő biztonsági kell konfigurálnia. A szolgáltatás része a Microsoft Azure SQL Database rugalmas méretezési funkció. További információkért lásd: [rugalmas méretezési felosztása és egyesítése Service-oktatóanyag](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Tanúsítványok beállítása
Tanúsítványokat kétféleképpen lehet konfigurálni. 

1. [Az SSL-tanúsítvány konfigurálása](#to-configure-the-ssl-certificate)
2. [Ügyfél-tanúsítványok konfigurálása](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Tanúsítványok beszerzése
Tanúsítványokat nyilvános hitelesítésszolgáltatótól (CA), vagy a szerezhető a [Windows tanúsítványszolgáltatást](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Ezek azok az előnyben részesített módszert szerezzenek be tanúsítványokat.

Ha ezek a lehetőségek nem érhetők el, létrehozhat **önaláírt tanúsítványokat**.

## <a name="tools-to-generate-certificates"></a>Eszközök tanúsítványainak létrehozásához szükséges
* [makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Az eszközök futtatása
* Az a fejlesztői parancssort. Ehhez a vizuális Studios lásd [Visual Studio parancssorból](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    Ha telepítve van, folytassa a:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* A WDK az első [Windows 8.1: Készletek és eszközök letöltése](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Az SSL-tanúsítvány konfigurálása
SSL-tanúsítvány szükséges a kommunikáció titkosításához, és a kiszolgáló hitelesítésére. Válassza ki a legmegfelelőbb az alábbi három forgatókönyv, és hajtsa végre az összes lépését:

### <a name="create-a-new-self-signed-certificate"></a>Hozzon létre egy új önaláírt tanúsítványt
1. [Önaláírt tanúsítvány létrehozása](#create-a-self-signed-certificate)
2. [Az önaláírt SSL-tanúsítvány PFX-fájl létrehozása](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Felhőszolgáltatás SSL-tanúsítvány feltöltése](#upload-ssl-certificate-to-cloud-service)
4. [A szolgáltatás konfigurációs fájlja SSL-tanúsítvány frissítése](#update-ssl-certificate-in-service-configuration-file)
5. [SSL-hitelesítésszolgáltató importálása](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Létező tanúsítvány használatára a tanúsítványtárolóból
1. [Tanúsítvány Store SSL-tanúsítvány exportálása](#export-ssl-certificate-from-certificate-store)
2. [Felhőszolgáltatás SSL-tanúsítvány feltöltése](#upload-ssl-certificate-to-cloud-service)
3. [A szolgáltatás konfigurációs fájlja SSL-tanúsítvány frissítése](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Létező tanúsítvány használatára a PFX-fájl
1. [Felhőszolgáltatás SSL-tanúsítvány feltöltése](#upload-ssl-certificate-to-cloud-service)
2. [A szolgáltatás konfigurációs fájlja SSL-tanúsítvány frissítése](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Ügyfél-tanúsítványok konfigurálása
Ügyféltanúsítványok szükségesek ahhoz, hogy a szolgáltatás-kérelmek hitelesítéséhez. Válassza ki a legmegfelelőbb az alábbi három forgatókönyv, és hajtsa végre az összes lépését:

### <a name="turn-off-client-certificates"></a>Kapcsolja ki az ügyfél-tanúsítványok
1. [Ügyfél-alapú hitelesítés kikapcsolása](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Új ügyfél önaláírt tanúsítványokat
1. [Hozzon létre egy önaláírt hitelesítésszolgáltató](#create-a-self-signed-certification-authority)
2. [A szolgáltatás felhőalapú hitelesítésszolgáltató-tanúsítvány feltöltése](#upload-ca-certificate-to-cloud-service)
3. [A szolgáltatás konfigurációs fájlja a Hitelesítésszolgáltatói tanúsítvány frissítése](#update-ca-certificate-in-service-configuration-file)
4. [Ügyfél-tanúsítványok kiállításához](#issue-client-certificates)
5. [Az ügyféltanúsítványok PFX-fájlok létrehozása](#create-pfx-files-for-client-certificates)
6. [Ügyfél-tanúsítvány importálása](#Import-Client-Certificate)
7. [Másolja a tanúsítvány-ujjlenyomatok ügyfél](#copy-client-certificate-thumbprints)
8. [A konfigurációs fájlban engedélyezett ügyfelek konfigurálása](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Meglévő ügyfél-tanúsítványok használata
1. [Keresse meg a hitelesítésszolgáltató nyilvános kulcsa](#find-ca-public-key)
2. [A szolgáltatás felhőalapú hitelesítésszolgáltató-tanúsítvány feltöltése](#Upload-CA-certificate-to-cloud-service)
3. [A szolgáltatás konfigurációs fájlja a Hitelesítésszolgáltatói tanúsítvány frissítése](#Update-CA-Certificate-in-Service-Configuration-File)
4. [Másolja a tanúsítvány-ujjlenyomatok ügyfél](#Copy-Client-Certificate-Thumbprints)
5. [A konfigurációs fájlban engedélyezett ügyfelek konfigurálása](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurálja az ügyfél tanúsítvány visszavonási ellenőrzése](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
A Szolgáltatásvégpontok való hozzáférés korlátozható az IP-címek meghatározott tartományát.

## <a name="to-configure-encryption-for-the-store"></a>A store titkosításának beállítása
A tanúsítvány a metaadat-tároló tárolt hitelesítő adatok titkosításához szükséges. Válassza ki a legmegfelelőbb az alábbi három forgatókönyv, és hajtsa végre az összes lépését:

### <a name="use-a-new-self-signed-certificate"></a>Egy új önaláírt tanúsítvány használata
1. [Önaláírt tanúsítvány létrehozása](#create-a-self-signed-certificate)
2. [Önaláírt titkosítási tanúsítvány PFX-fájl létrehozása](#create-pfx-file-for-self-signed-ssl-certificate)
3. [A felhőalapú szolgáltatás titkosítási tanúsítvány feltöltése](#upload-encryption-certificate-to-cloud-service)
4. [Titkosítási tanúsítvány frissítése a szolgáltatáskonfigurációs fájlban](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Használhat egy meglévő tanúsítványt a tanúsítványtárolóból
1. [Tanúsítvány Store a titkosítási tanúsítvány exportálása](#export-encryption-certificate-from-certificate-store)
2. [A felhőalapú szolgáltatás titkosítási tanúsítvány feltöltése](#upload-encryption-certificate-to-cloud-service)
3. [Titkosítási tanúsítvány frissítése a szolgáltatáskonfigurációs fájlban](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Létező tanúsítvány használatára a PFX-fájl
1. [A felhőalapú szolgáltatás titkosítási tanúsítvány feltöltése](#upload-encryption-certificate-to-cloud-service)
2. [Titkosítási tanúsítvány frissítése a szolgáltatáskonfigurációs fájlban](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Az alapértelmezett konfiguráció
Az alapértelmezett konfiguráció összes megtagadja végpontjához. Ez az az ajánlott beállítás, mivel a kéréseket a végpontokkal is elvégezheti a bizalmas információkat, például adatbázis-hitelesítő adatok.
Az alapértelmezett konfiguráció lehetővé teszi, hogy a HTTPS-végpont az elérésére. Ez a beállítás további korlátozott lehet.

### <a name="changing-the-configuration"></a>A konfiguráció módosítása
A alkalmazni a hozzáférés-vezérlési szabályok és a végpont csoport konfigurált a **<EndpointAcls>** című rész a **szolgáltatás konfigurációs fájlja**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

A hozzáférés-vezérlési csoportban szabályok úgy vannak konfigurálva, az egy <AccessControl name=""> szakaszában a szolgáltatás konfigurációs fájlja. 

A formátum ismertetését a hálózati hozzáférés-vezérlési listák dokumentációját.
Például ahhoz, hogy csak IP-címek 100.100.0.0 való 100.100.255.255 a HTTPS-végpontot a tartományban, a szabályok kellene kinéznie:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>Szolgáltatás megelőzési szolgáltatásmegtagadás
Van két különböző mechanizmus támogatott észlelése és a szolgáltatásmegtagadásos támadások megelőzése érdekében:

* Korlátozhatja a távoli gazdagépenként egyidejű kérelmek száma (alapértelmezés szerint kikapcsolva)
* Távoli gazdagépenként hozzáférési sebesség korlátozása (az alapértelmezés szerint)

Ezek a funkciók további részletes ismertetését lásd: az IIS-ben a dinamikus IP-biztonság alapul. Ha ez a konfiguráció módosítása ügyeljen arra, hogy a következő tényezőket:

* Proxyk és a távoli állomás információkat a hálózati címfordítás eszközök működését.
* Minden kérelmet a webes szerepkör az összes erőforrást számít (például parancsfájlok, képek és egyéb betöltése)

## <a name="restricting-number-of-concurrent-accesses"></a>Egyidejű hozzáférések számának korlátozása
Az ezt a viselkedést konfiguráló beállítások a következők:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Ez a védelem engedélyezéséhez a true DynamicIpRestrictionDenyByConcurrentRequests módosítsa.

## <a name="restricting-rate-of-access"></a>Hozzáférési sebesség korlátozása
Az ezt a viselkedést konfiguráló beállítások a következők:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>A letiltott irányuló kérelemre adott válasz konfigurálása
A következő beállítást konfigurálja a megtagadott irányuló kérelemre adott válasz:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Tekintse meg a dokumentációt más támogatott értékei a dinamikus IP-biztonság az IIS-ben.

## <a name="operations-for-configuring-service-certificates"></a>Operations for service-tanúsítványok konfigurálása
Ez a témakör csak referenciaként van. Kövesse az ismertetett konfigurációs lépései:

* Az SSL-tanúsítvány konfigurálása
* Ügyfél-tanúsítványok konfigurálása

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása
Hajtsa végre:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Testreszabása:

* -n a szolgáltatás URL-címet. A helyettesítő karakterek ("CN = * .cloudapp .net") és az alternatív neveket ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") támogatottak.
* -e a tanúsítvány lejárati dátummal hozzon létre egy erős jelszót, és adja meg, amikor a rendszer kéri.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Az önaláírt SSL-tanúsítvány PFX-fájl létrehozása
Hajtsa végre:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Adja meg a jelszót, és ezután exportálja a tanúsítványt ezekkel a beállításokkal:

* Igen, a titkos kulcs exportálása
* Minden további tulajdonság exportálása

## <a name="export-ssl-certificate-from-certificate-store"></a>SSL-tanúsítvány exportálása a tanúsítványtárolóból
* Keresse meg a tanúsítvány
* Kattintson a műveletek összes -> feladatok -> Exportálás...
* Exportálja a tanúsítványt egy. PFX-fájl ezekkel a beállításokkal:
  * Igen, a titkos kulcs exportálása
  * Minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges * minden további tulajdonság exportálása

## <a name="upload-ssl-certificate-to-cloud-service"></a>Felhőszolgáltatás SSL-tanúsítvány feltöltése
Feltöltés a a meglévő tanúsítványt, vagy jön létre. Az SSL-kulcspárt a PFX-fájlt:

* Adja meg a jelszót a titkos kulcs adataival védelme

## <a name="update-ssl-certificate-in-service-configuration-file"></a>A szolgáltatás konfigurációs fájlja SSL-tanúsítvány frissítése
Frissítse a konfigurációs fájlban a következő beállítást ujjlenyomat értékét az a felhőszolgáltatásba a tanúsítvány ujjlenyomata:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>SSL-hitelesítésszolgáltató importálása
Minden fiók/gép, amely fog kommunikálni a szolgáltatás az alábbi lépéseket követve:

* Kattintson duplán a. CER-fájlt a Windows Intézőben
* A tanúsítvány párbeszédpanelen kattintson a tanúsítvány telepítése...
* Importálja a tanúsítványt a megbízható legfelső szintű hitelesítésszolgáltatók tárolójába.

## <a name="turn-off-client-certificate-based-authentication"></a>Ügyfél-alapú hitelesítés kikapcsolása
Csak az ügyfél tanúsítványalapú hitelesítést is támogatja, és letiltásával lehetővé teszi a nyilvános hozzáférés szolgáltatásvégpontokra, kivéve, ha más mechanizmusok nem helyben (például a Microsoft Azure virtuális hálózat esetén).

Ezek a beállítások módosítása a konfigurációs fájlban a szolgáltatás kikapcsolásához hamis értékre:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Majd másolja a Hitelesítésszolgáltatói tanúsítvány beállítás SSL-tanúsítványt ugyanazzal az ujjlenyomattal:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Hozzon létre egy önaláírt hitelesítésszolgáltató
Hajtsa végre az alábbi lépéseket egy hitelesítésszolgáltató segítségével önaláírt tanúsítvány létrehozása:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

A Testreszabás

* -e a tanúsítvány lejárati dátuma

## <a name="find-ca-public-key"></a>Keresse meg a hitelesítésszolgáltató nyilvános kulcsa
Minden tanúsítványt kell lettek kibocsátva a szolgáltatás által megbízhatónak minősített hitelesítésszolgáltató által. Keresse meg a nyilvános kulcsot a az ügyfél, amelyek annak érdekében, hogy töltse fel a felhőszolgáltatáshoz való hitelesítéshez használt tanúsítványokat kiállító hitelesítésszolgáltatóhoz.

Ha nem érhető el a fájlt a nyilvános kulccsal, exportálja a tanúsítványtárolóból:

* Keresse meg a tanúsítvány
  * Keresse meg ugyanazt a hitelesítésszolgáltató által kiadott ügyféltanúsítvány
* Kattintson duplán a tanúsítványra.
* A tanúsítvány párbeszédpanelen válassza ki a Tanúsítványlánc lap.
* Kattintson duplán a hitelesítésszolgáltató az elérési út.
* Jegyzeteket a tanúsítvány tulajdonságai.
* Zárja be a **tanúsítvány** párbeszédpanel.
* Keresse meg a tanúsítvány
  * Keresse meg a fentebb feltüntetett hitelesítésszolgáltató.
* Kattintson a műveletek összes -> feladatok -> Exportálás...
* Exportálja a tanúsítványt egy. CER ezekkel a beállításokkal:
  * **Nem, nem akarom exportálni a titkos kulcs**
  * Minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges.
  * Minden további tulajdonság exportálása.

## <a name="upload-ca-certificate-to-cloud-service"></a>Felhőszolgáltatáshoz CA-tanúsítvány feltöltése
Feltöltés a a meglévő tanúsítványt, vagy jön létre. CER-fájlt a hitelesítésszolgáltató nyilvános kulccsal.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Frissítés Hitelesítésszolgáltatói tanúsítvány szolgáltatáskonfigurációs fájlban
Frissítse a konfigurációs fájlban a következő beállítást ujjlenyomat értékét az a felhőszolgáltatásba a tanúsítvány ujjlenyomata:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Frissítse az alábbi beállítás értéke az azonos ujjlenyomattal rendelkező:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Ügyfél-tanúsítványok kiállítása
Minden egyes jogosult hozzáférni a szolgáltatáshoz kizárólagos használatú ügyféltanúsítvánnyal kell rendelkeznie, és a saját erős jelszót a titkos kulcs védelme érdekében érdemes választania. 

Ahol a önaláírt hitelesítésszolgáltató tanúsítványát előállított és tárolt ugyanarra a gépre a következő lépéseket kell végrehajtani:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Testreszabása:

* -n-azonosítót az ügyfélnek, amely a tanúsítvány hitelesít
* -e a tanúsítvány lejárati dátuma
* MyID.pvk és az ezt a tanúsítványt az egyedi fájlnevek MyID.cer

Ezzel a paranccsal létrehozott és többször használt jelszó megadását fogja kérni. Használjon erős jelszót.

## <a name="create-pfx-files-for-client-certificates"></a>Az ügyfél PFX-fájlok tanúsítványok létrehozása
Minden egyes létrehozott ügyféltanúsítványt hajtsa végre:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Testreszabása:

    MyID.pvk and MyID.cer with the filename for the client certificate

Adja meg a jelszót, és ezután exportálja a tanúsítványt ezekkel a beállításokkal:

* Igen, a titkos kulcs exportálása
* Minden további tulajdonság exportálása
* A személy, akinek a tanúsítvány kiállítását az exportálási jelszót kell választania

## <a name="import-client-certificate"></a>Ügyfél-tanúsítvány importálása
Minden egyes személy, akinek ügyfél-tanúsítvány kibocsátása megtörtént a kulcspár szeretnének a szolgáltatással kommunikáló gépeken kell importálni:

* Kattintson duplán a. PFX-fájlt a Windows Intézőben
* Importálás be személyes tanúsítványtároló a legalább ezt a beállítást:
  * Tartalmazza az összes kiterjesztett tulajdonság be van jelölve

## <a name="copy-client-certificate-thumbprints"></a>Másolja a tanúsítvány-ujjlenyomatok ügyfél
Minden egyes személy, akinek ügyféltanúsítvány adtak ki annak érdekében, hogy a tanúsítványt, amely megjelenik a szolgáltatás konfigurációs fájlja ujjlenyomatának beszerzéséhez kövesse az alábbi lépéseket:

* Run certmgr.exe
* Válassza ki a személyes lap
* Kattintson duplán a hitelesítéshez használandó ügyféltanúsítványt
* A tanúsítvány a megnyíló párbeszédpanelen válassza a részleteket tartalmazó lapot
* Ellenőrizze, hogy megjelenítése az összes megjelenítése
* Válassza ki a listában ujjlenyomat nevű mező
* Másolja az értéket az ujjlenyomat ** törlése előtt az első számot nem látható Unicode-karaktereket ** minden szóközt törlése

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>A konfigurációs fájlban engedélyezett ügyfelek konfigurálása
Frissítse a konfigurációs fájlban a következő beállítás értékét az hozzáférhessen-e a szolgáltatás ügyfél-tanúsítványok ujjlenyomatai vesszővel tagolt listája:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Konfigurálja az ügyfél tanúsítvány visszavonási ellenőrzése
Az alapértelmezett beállítás nem ellenőrzi az ügyfél tanúsítvány visszavonási állapotának a hitelesítésszolgáltatóhoz. Az ellenőrzések bekapcsolása, ha a hitelesítésszolgáltató által kibocsátott tanúsítványok az ügyfél támogatja az ilyen ellenőrzéseket, módosítsa a következő beállítást egy, a X509RevocationMode enumerálása a megadott:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>PFX-fájlt a titkosítási önaláírt tanúsítványok létrehozása
A titkosítási tanúsítványt hajtsa végre:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Testreszabása:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Adja meg a jelszót, és ezután exportálja a tanúsítványt ezekkel a beállításokkal:

* Igen, a titkos kulcs exportálása
* Minden további tulajdonság exportálása
* A jelszó kell a tanúsítványt a felhőszolgáltatáshoz való feltöltésekor.

## <a name="export-encryption-certificate-from-certificate-store"></a>Titkosítási tanúsítvány exportálása a tanúsítványtárolóból
* Keresse meg a tanúsítvány
* Kattintson a műveletek összes -> feladatok -> Exportálás...
* Exportálja a tanúsítványt egy. PFX-fájl ezekkel a beállításokkal: 
  * Igen, a titkos kulcs exportálása
  * Minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges 
* Minden további tulajdonság exportálása

## <a name="upload-encryption-certificate-to-cloud-service"></a>A felhőalapú szolgáltatás titkosítási tanúsítvány feltöltése
Feltöltés a a meglévő tanúsítványt, vagy jön létre. A titkosítási kulcspárt a PFX-fájlt:

* Adja meg a jelszót a titkos kulcs adataival védelme

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Titkosítási tanúsítvány frissítése a szolgáltatáskonfigurációs fájlban
Az ujjlenyomat értékét az alábbi beállítások a konfigurációs fájlban frissítse a felhőszolgáltatásba a tanúsítvány ujjlenyomata:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Tanúsítvány-műveletek
* Az SSL-tanúsítvány konfigurálása
* Ügyfél-tanúsítványok konfigurálása

## <a name="find-certificate"></a>Keresse meg a tanúsítvány
Kövesse az alábbi lépéseket:

1. Mmc.exe futtassa.
2. File -> Add/Remove Snap-in…
3. Válassza ki **tanúsítványok**.
4. Kattintson a **Hozzáadás** parancsra.
5. Válassza ki a tanúsítványtár helye.
6. Kattintson a **Befejezés** gombra.
7. Kattintson az **OK** gombra.
8. Bontsa ki a **tanúsítványok**.
9. Bontsa ki a tanúsítványt tároló csomópontot.
10. Bontsa ki a tanúsítványa gyermek csomópont.
11. Válasszon ki egy tanúsítványt a listában.

## <a name="export-certificate"></a>Tanúsítvány exportálása
Az a **tanúsítvány exportálása varázslóban**:

1. Kattintson a **tovább**.
2. Válassza ki **Igen**, majd **a titkos kulcs exportálásának**.
3. Kattintson a **tovább**.
4. Válassza ki a kívánt kimeneti fájl formátumát.
5. Ellenőrizze a kívánt beállításokat.
6. Ellenőrizze **jelszó**.
7. Adjon meg egy erős jelszót, és erősítse meg.
8. Kattintson a **tovább**.
9. Írja be vagy tallózással keresse meg a FileName paramétert a tanúsítvány tárolására (használja a. PFX-kiterjesztéssel).
10. Kattintson a **tovább**.
11. Kattintson a **Befejezés** gombra.
12. Kattintson az **OK** gombra.

## <a name="import-certificate"></a>Tanúsítvány importálása
A Tanúsítványimportáló varázslóban:

1. Válassza ki a tároló helyét.
   
   * Válassza ki **aktuális felhasználó** Ha csak az aktuális felhasználóhoz tartozó futó folyamatok a szolgáltatás elérésére
   * Válassza ki **helyi gép** , ha a számítógép más folyamatok a szolgáltatás elérésére
2. Kattintson a **tovább**.
3. Ha importál egy fájlból, erősítse meg a fájl elérési útját.
4. Ha egy. PFX-fájlt:
   1. Adja meg a jelszót a titkos kulcsok védelme
   2. Importálási beállítások
5. Válassza ki a "Hely" tanúsítványok ebben a tárolóban
6. Kattintson a **Browse** (Tallózás) gombra.
7. Válassza ki a kívánt tárolót.
8. Kattintson a **Befejezés** gombra.
   
   * Ha a megbízható legfelső szintű hitelesítésszolgáltatóinak választotta, kattintson a **Igen**.
9. Kattintson a **OK** párbeszédpanelen az összes windows rendszeren.

## <a name="upload-certificate"></a>Tanúsítvány feltöltése
Az [Azure Portalon](https://portal.azure.com/)

1. Válassza ki **Cloud Services**.
2. Válassza ki a felhőszolgáltatást.
3. A felső menüben kattintson a **tanúsítványok**.
4. Kattintson az alsó sávján **feltöltése**.
5. Válassza ki a tanúsítványfájl.
6. Ha egy. PFX adja meg a jelszót a titkos kulcs.
7. Ha kész, másolja a tanúsítvány ujjlenyomatát az új bejegyzést a listában.

## <a name="other-security-considerations"></a>Más biztonsági szempontok
A jelen dokumentumban ismertetett SSL-beállítások a szolgáltatás és az ügyfelek közötti kommunikáció titkosításához, ha a HTTPS-végpontokat használják. Ez azért fontos óta adatbázis-hozzáférési hitelesítő adatokat, és lehetséges, hogy más bizalmas információ található a kommunikációt. Ne feledje, hogy a szolgáltatás továbbra is fennáll belső állapot, a belső táblát is a Microsoft Azure SQL Database a Microsoft Azure-előfizetésében a metaadat-tároló megadott hitelesítő adatokat, beleértve. Az adatbázishoz a szolgáltatáskonfigurációs fájlban a következő beállítás részeként lett definiálva (. CSCFG-fájl): 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Ebben az adatbázisban tárolt hitelesítő adatok titkosítása. Azonban ajánlott eljárásként, győződjön meg arról, hogy a webes és feldolgozói szerepkörök a szolgáltatástelepítések a rendszer mindig naprakész és biztonságos, mint azok is rendelkezik hozzáféréssel a metaadatokat tároló adatbázis és az tárolt hitelesítő adatok titkosításához és visszafejtéséhez használt tanúsítvány. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

