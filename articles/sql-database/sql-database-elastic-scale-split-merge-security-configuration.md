---
title: "Vegyes egyesítéses biztonsági beállításai |} Microsoft Docs"
description: "Állítson be x409 tanúsítványok titkosítási a vegyes/egyesítés szolgáltatással a rugalmas méretezést."
metakeywords: Elastic Database certificates security
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
ms.assetid: f9e89c57-61a0-484f-b787-82dae2349cb6
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: torsteng
ms.openlocfilehash: 98f046a943c1fe33b421b3ab6830d78e6c4304a0
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="split-merge-security-configuration"></a>Vegyes egyesítéses biztonsági konfiguráció
A felosztott/egyesítés szolgáltatással biztonsági megfelelően be kell állítania. A szolgáltatás a Microsoft Azure SQL Database a rugalmas bővítést szolgáltatás részét képezi. További információkért lásd: [rugalmas méretezési felosztása és egyesítése szolgáltatás oktatóanyag](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Tanúsítványok konfigurálása
Két módon a tanúsítványok konfigurálva legyenek. 

1. [Az SSL-tanúsítvány konfigurálása](#to-configure-the-ssl-certificate)
2. [Ügyfél-tanúsítványok konfigurálása](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Tanúsítványok beszerzése
Tanúsítványok vagy a nyilvános hitelesítésszolgáltatótól (CA) szerezhetők a [Windows tanúsítványszolgáltatást](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Ezek a tanúsítványok megszerzését előnyben részesített módszert.

Ha ezek a lehetőségek nem állnak rendelkezésre, létrehozhat **önaláírt tanúsítványokat**.

## <a name="tools-to-generate-certificates"></a>Eszközök tanúsítványainak létrehozásához
* [MakeCert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Az eszközök futtatása
* Az a fejlesztő parancssorból Visual Studios, lásd: [Visual Studio parancssorból](http://msdn.microsoft.com/library/ms229859.aspx) 
  
    Ha telepítette, Ugrás:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* A WDK az beszerzése [Windows 8.1: Töltse le a készletek és eszközök](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Az SSL-tanúsítvány konfigurálása
Titkosítja a kommunikációt, és a kiszolgáló hitelesítésére az SSL-tanúsítvány szükséges. Válassza ki a legmegfelelőbb az alábbi három forgatókönyv, és hajtsa végre az összes lépését:

### <a name="create-a-new-self-signed-certificate"></a>Hozzon létre egy új önaláírt tanúsítványt
1. [Önaláírt tanúsítvány létrehozása](#create-a-self-signed-certificate)
2. [Az önaláírt SSL-tanúsítvány PFX-fájl létrehozása](#create-pfx-file-for-self-signed-ssl-certificate)
3. [A felhőalapú szolgáltatás SSL-tanúsítvány feltöltése](#upload-ssl-certificate-to-cloud-service)
4. [A szolgáltatás konfigurációs fájljában SSL-tanúsítvány frissítése](#update-ssl-certificate-in-service-configuration-file)
5. [SSL-hitelesítésszolgáltató importálása](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Létező tanúsítvány használatára a tanúsítványtárolóból
1. [SSL-tanúsítvány exportálása a tanúsítványt a tanúsítványtárolóból](#export-ssl-certificate-from-certificate-store)
2. [A felhőalapú szolgáltatás SSL-tanúsítvány feltöltése](#upload-ssl-certificate-to-cloud-service)
3. [A szolgáltatás konfigurációs fájljában SSL-tanúsítvány frissítése](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Létező tanúsítvány használatára a PFX-fájl
1. [A felhőalapú szolgáltatás SSL-tanúsítvány feltöltése](#upload-ssl-certificate-to-cloud-service)
2. [A szolgáltatás konfigurációs fájljában SSL-tanúsítvány frissítése](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Ügyfél-tanúsítványok konfigurálása
Ügyféltanúsítványok szükségesek a szolgáltatás-kérelmek hitelesítéséhez szükséges. Válassza ki a legmegfelelőbb az alábbi három forgatókönyv, és hajtsa végre az összes lépését:

### <a name="turn-off-client-certificates"></a>Ügyféltanúsítványok kikapcsolása
1. [Ügyféltanúsítvány-alapú hitelesítés kikapcsolása](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Új ügyfél önaláírt tanúsítványokat
1. [Hozzon létre egy önaláírt hitelesítésszolgáltató](#create-a-self-signed-certification-authority)
2. [A felhőalapú szolgáltatás CA-tanúsítvány feltöltése](#upload-ca-certificate-to-cloud-service)
3. [A szolgáltatás konfigurációs fájljában Hitelesítésszolgáltatói tanúsítvány frissítése](#update-ca-certificate-in-service-configuration-file)
4. [Ügyfél-tanúsítványok kiállításához](#issue-client-certificates)
5. [Az ügyféltanúsítványok PFX-fájlok létrehozása](#create-pfx-files-for-client-certificates)
6. [Ügyfél-tanúsítvány importálása](#Import-Client-Certificate)
7. [Másolja a tanúsítvány-ujjlenyomatok Client](#copy-client-certificate-thumbprints)
8. [A szolgáltatás konfigurációs fájljában engedélyezett ügyfelek konfigurálása](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Meglévő ügyfél-tanúsítványok használata
1. [Nyilvános hitelesítésszolgáltató-kulcs megkeresése](#find-ca-public-key)
2. [A felhőalapú szolgáltatás CA-tanúsítvány feltöltése](#Upload-CA-certificate-to-cloud-service)
3. [A szolgáltatás konfigurációs fájljában Hitelesítésszolgáltatói tanúsítvány frissítése](#Update-CA-Certificate-in-Service-Configuration-File)
4. [Másolja a tanúsítvány-ujjlenyomatok Client](#Copy-Client-Certificate-Thumbprints)
5. [A szolgáltatás konfigurációs fájljában engedélyezett ügyfelek konfigurálása](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurálja az ügyfél visszavonási állapotának ellenőrzése](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
A végpontok való hozzáférés korlátozható adott IP-címek tartományát.

## <a name="to-configure-encryption-for-the-store"></a>A tároló titkosítás konfigurálása
A metaadat-tárolóban tárolt hitelesítő adatok titkosításához tanúsítvány szükséges. Válassza ki a legmegfelelőbb az alábbi három forgatókönyv, és hajtsa végre az összes lépését:

### <a name="use-a-new-self-signed-certificate"></a>Egy új önaláírt tanúsítvány használatára
1. [Önaláírt tanúsítvány létrehozása](#create-a-self-signed-certificate)
2. [Hozzon létre önaláírt titkosítási tanúsítvány PFX-fájlból](#create-pfx-file-for-self-signed-ssl-certificate)
3. [A felhőalapú szolgáltatás titkosítási tanúsítvány feltöltése](#upload-encryption-certificate-to-cloud-service)
4. [A szolgáltatás konfigurációs fájljában titkosítási tanúsítvány frissítése](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Használhat egy meglévő tanúsítványt a tanúsítványtárolóból
1. [Titkosítási tanúsítvány exportálása a tanúsítványt a tanúsítványtárolóból](#export-encryption-certificate-from-certificate-store)
2. [A felhőalapú szolgáltatás titkosítási tanúsítvány feltöltése](#upload-encryption-certificate-to-cloud-service)
3. [A szolgáltatás konfigurációs fájljában titkosítási tanúsítvány frissítése](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Létező tanúsítvány használatára a PFX-fájl
1. [A felhőalapú szolgáltatás titkosítási tanúsítvány feltöltése](#upload-encryption-certificate-to-cloud-service)
2. [A szolgáltatás konfigurációs fájljában titkosítási tanúsítvány frissítése](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Az alapértelmezett konfiguráció
Az alapértelmezett konfiguráció összes megtagadja végpontjához. Ez az ajánlott beállítása, mivel a fenti végpontokkal való kérelmek bizalmas információkat, például adatbázis-hitelesítő adatok is elvégezheti.
Az alapértelmezett konfiguráció lehetővé teszi, hogy a HTTPS-végpont az elérésére. Ezzel a beállítással korlátozható tovább.

### <a name="changing-the-configuration"></a>A konfiguráció módosítása
A csoport vonatkozó hozzáférés-vezérlési szabályok és a végpont vannak konfigurálva a  **<EndpointAcls>**  szakasz a **szolgáltatás konfigurációs fájlja**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

A szabályok hozzáférés-vezérlési csoportban vannak konfigurálva a egy <AccessControl name=""> a szolgáltatás konfigurációs fájl részét. 

A formátum esetén, tekintse meg a hálózati hozzáférés-vezérlési listái dokumentációjában.
Például ahhoz, hogy csak IP-cím a tartomány 100.100.0.0 való 100.100.255.255 a HTTPS-végpont elérésére, a szabályok fognak kinézni:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>Letiltja a szolgáltatás megelőzése
Két különböző mechanizmus észleli, és szolgáltatásmegtagadási támadások megelőzése érdekében támogatott van:

* Korlátozhatja a távoli állomásonként egyidejű kérelmek száma (alapértelmezés szerint kikapcsolva)
* Távoli állomásonként hozzáférési sebesség korlátozása (az alapértelmezés)

Ezek a funkciók további részletes ismertetését lásd: az IIS-ben a dinamikus IP-biztonság alapul. Ha ez a konfiguráció módosítása ügyeljen arra a következő tényezőket:

* Proxyk és a hálózati címfordítás eszközök a távoli állomás információ fölé működését
* A webes szerepkörben lévő erőforrásokhoz kéréseknek tekinthető (pl. betöltése parancsfájlok, képek, stb)

## <a name="restricting-number-of-concurrent-accesses"></a>Korlátozza az egyidejű hozzáférések száma
Ez a viselkedés konfigurált a beállítások a következők:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Ez a védelem engedélyezéséhez a true DynamicIpRestrictionDenyByConcurrentRequests módosítható.

## <a name="restricting-rate-of-access"></a>Gyakorisága a hozzáférés korlátozása
Ez a viselkedés konfigurált a beállítások a következők:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>A visszautasított kérelemre válaszolva konfigurálása
A következő beállítással a egy letiltott irányuló kérelemre adott válasz:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Tekintse meg a dinamikus IP-biztonság az IIS-ben más támogatott értékek dokumentációját.

## <a name="operations-for-configuring-service-certificates"></a>Műveletek a szolgáltatás tanúsítványok konfigurálásáról
Ez a témakör csak a hivatkozás van. Hajtsa végre a leírt konfigurációs lépéseket:

* Az SSL-tanúsítvány konfigurálása
* Állítson be ügyféltanúsítványokat

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása
Hajtható végre:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Testreszabása:

* -n a szolgáltatási URL-cím. Helyettesítő karakterek ("CN = * .cloudapp .net") és az alternatív neveket ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") támogatottak.
* -e a tanúsítvány lejárati dátummal hozzon létre egy erős jelszót, és adja meg, amikor a rendszer kéri.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Hozzon létre önaláírt SSL-tanúsítvány PFX-fájlt
Hajtható végre:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Adja meg a jelszót, és exportálhatja a tanúsítványt, amelynek ezeket a beállításokat:

* Igen, a titkos kulcs exportálását választom
* Minden további tulajdonság exportálása

## <a name="export-ssl-certificate-from-certificate-store"></a>SSL-tanúsítvány exportálása a tanúsítványt a tanúsítványtárolóból
* Tanúsítvány található
* Kattintson a műveletek összes -> feladatok -> Exportálás...
* Exportálja a tanúsítványt egy. Ezek a beállítások a PFX-fájlt:
  * Igen, a titkos kulcs exportálását választom
  * Minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges * minden további tulajdonság exportálása

## <a name="upload-ssl-certificate-to-cloud-service"></a>A felhőalapú szolgáltatás SSL-tanúsítvány feltöltése
Feltöltés a a meglévő tanúsítványt, vagy jön létre. Az SSL-kulcsból álló kulcspárt a PFX-fájlt:

* Adja meg a jelszót a titkos kulcs adatainak védelme

## <a name="update-ssl-certificate-in-service-configuration-file"></a>A szolgáltatás konfigurációs fájljában SSL-tanúsítvány frissítése
Frissítse a szolgáltatás konfigurációs fájljában a következő beállítást ujjlenyomat értékének tölteni a felhőalapú szolgáltatáshoz a tanúsítvány-ujjlenyomata:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>SSL-hitelesítésszolgáltató importálása
Kövesse az alábbi lépéseket az összes fiók/gépben fog kommunikálni a szolgáltatás:

* Kattintson duplán a. A Windows Intézőben CER-fájljával
* A tanúsítvány párbeszédpanelen kattintson a tanúsítvány telepítése...
* Importálja a tanúsítványt a megbízható legfelső szintű hitelesítésszolgáltatók tárolójába.

## <a name="turn-off-client-certificate-based-authentication"></a>Ügyféltanúsítvány-alapú hitelesítés kikapcsolása
Csak az ügyféltanúsítvány-alapú hitelesítés támogatott, és letiltja azt lehetővé szolgáltatásvégpontokra, nyilvánosan elérhető kivéve, ha más mechanizmusok helyen (pl. Microsoft Azure virtuális hálózat).

Módosítsa a beállításokat a szolgáltatás konfigurációs fájljában a szolgáltatás kikapcsolásához hamis:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Ezután másolja a Hitelesítésszolgáltatói tanúsítvány beállítás SSL-tanúsítványt ugyanazzal az ujjlenyomattal:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Hozzon létre egy önaláírt hitelesítésszolgáltató
Hajtsa végre a következő lépésekkel hozza létre a hitelesítésszolgáltató meghatalmazottjaként járhatnak el egy önaláírt tanúsítványt:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Testreszabása

* -e a tanúsítvány lejárati dátuma

## <a name="find-ca-public-key"></a>Nyilvános hitelesítésszolgáltató-kulcs megkeresése
Minden ügyfél kell kiadott tanúsítványok a szolgáltatás megbízható hitelesítésszolgáltató által. A nyilvános kulcsát a az ügyfelet, hogy hitelesítésre használt töltse fel a felhőszolgáltatás a tanúsítványokat kiállító hitelesítésszolgáltató található.

Ha nem érhető el a fájlt a nyilvános kulccsal, exportálja a tanúsítványtárolóból:

* Tanúsítvány található
  * Keresse meg az azonos hitelesítésszolgáltató által kiállított ügyféltanúsítványt
* Kattintson duplán a tanúsítványra.
* A tanúsítvány párbeszédpanelen jelölje ki az Általános lapon.
* Kattintson duplán a hitelesítésszolgáltató az elérési út.
* A tanúsítvány tulajdonságainak jegyzeteket.
* Zárja be a **tanúsítvány** párbeszédpanel.
* Tanúsítvány található
  * Keresse meg a fent leírt hitelesítésszolgáltató.
* Kattintson a műveletek összes -> feladatok -> Exportálás...
* Exportálja a tanúsítványt egy. CER beállítások segítségével:
  * **Nem, nem akarom exportálni a titkos kulcs**
  * Minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges.
  * Minden további tulajdonság exportálása.

## <a name="upload-ca-certificate-to-cloud-service"></a>CA-tanúsítvány feltöltése a felhőalapú szolgáltatás
Feltöltés a a meglévő tanúsítványt, vagy jön létre. A hitelesítésszolgáltató nyilvános kulccsal CER-fájljával.

## <a name="update-ca-certificate-in-service-configuration-file"></a>A szolgáltatás konfigurációs fájljában frissítés Hitelesítésszolgáltatói tanúsítvány
Frissítse a szolgáltatás konfigurációs fájljában a következő beállítást ujjlenyomat értékének tölteni a felhőalapú szolgáltatáshoz a tanúsítvány-ujjlenyomata:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

A következő beállítás értékének módosítása ugyanazzal az ujjlenyomattal:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Ügyfél tanúsítványokat
Minden egyes, a szolgáltatás elérésére jogosult ügyféltanúsítvánnyal a his/hers kizárólagos használatára kell lennie, és erős jelszót a titkos kulcs védelme saját his/hers kell kiválasztani. 

Az azonos gépen, ahol az önaláírt hitelesítésszolgáltató-tanúsítvány jön létre és tárolja a következő lépéseket kell végrehajtani:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Testreszabása:

* az ügyfélnek, amely a tanúsítvány hitelesítése a azonosítójú -n
* -e a tanúsítvány lejárati dátuma
* MyID.pvk és MyID.cer, a egyedi fájlneveket az ügyféltanúsítványt

Ez a parancs felszólítja hozható létre, és többször használt jelszó. Használjon erős jelszót.

## <a name="create-pfx-files-for-client-certificates"></a>Az ügyfél PFX-fájlok tanúsítványok létrehozása
Minden létrehozott ügyféltanúsítványt hajtható végre:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Testreszabása:

    MyID.pvk and MyID.cer with the filename for the client certificate

Adja meg a jelszót, és exportálhatja a tanúsítványt, amelynek ezeket a beállításokat:

* Igen, a titkos kulcs exportálását választom
* Minden további tulajdonság exportálása
* Az egyes, akinek a tanúsítvány kiállítva kell kiválasztani az exportálási jelszó

## <a name="import-client-certificate"></a>Ügyfél-tanúsítvány importálása
Minden egyes személy, amely ügyféltanúsítványt adtak ki a gépeket többé a szolgáltatással való kommunikációra használja a kulcspár kell importálni:

* Kattintson duplán a. PFX-fájlt a Windows Intézőben
* Importálás azokat a személyes tanúsítványtároló a legalább ezt a beállítást:
  * Minden további tulajdonság be van jelölve szerepeltetése

## <a name="copy-client-certificate-thumbprints"></a>Másolja a tanúsítvány-ujjlenyomatok client
Minden egyes személy, amely ügyféltanúsítványt adtak ki kövesse az alábbi lépéseket ahhoz, hogy az beszerzése ujjlenyomatát his/hers tanúsítvány, amelyek nem kerülnek be a szolgáltatás konfigurációs fájlja:

* Certmgr.exe futtatása
* Válassza ki a személyes lap
* Kattintson duplán a hitelesítéshez használandó ügyféltanúsítványt
* A tanúsítvány megnyíló párbeszédpanelen válassza ki a részleteket tartalmazó lapot
* Győződjön meg arról, hogy megjeleníti az összes megjelenítése
* Válassza ki a listában ujjlenyomat nevű mező
* Másolja az ujjlenyomat értékének ** törlése előtt az első számjegy láthatatlan Unicode-karaktereket ** csak szóközökből törlése

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>A szolgáltatás konfigurációs fájljában engedélyezett ügyfelek konfigurálása
Frissítse az értéket, a szolgáltatás konfigurációs fájljában a következő beállítási hozzáférhetnek a szolgáltatás ügyfél-tanúsítványok ujjlenyomatai vesszővel tagolt listáját:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Konfigurálja az ügyfél visszavonási állapotának ellenőrzése
Az alapértelmezett beállítás nem ellenőrzi a hitelesítésszolgáltatóhoz, az ügyfél tanúsítvány-visszavonási állapotát. Az ellenőrzések bekapcsolása, ha az ügyfél-tanúsítványokat kiállító hitelesítésszolgáltató támogatja az ilyen ellenőrzések, a következő beállítás módosításával a X509RevocationMode számbavétel megadott értékek egyike:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Hozzon létre önaláírt titkosítási tanúsítványok PFX-fájlt
Titkosítási tanúsítványt az alábbi hajtható végre:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Testreszabása:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Adja meg a jelszót, és exportálhatja a tanúsítványt, amelynek ezeket a beállításokat:

* Igen, a titkos kulcs exportálását választom
* Minden további tulajdonság exportálása
* A jelszó kell a tanúsítvány feltöltésekor a rendszer a felhőszolgáltatásba.

## <a name="export-encryption-certificate-from-certificate-store"></a>Titkosítási tanúsítvány exportálása a tanúsítványt a tanúsítványtárolóból
* Tanúsítvány található
* Kattintson a műveletek összes -> feladatok -> Exportálás...
* Exportálja a tanúsítványt egy. Ezek a beállítások a PFX-fájlt: 
  * Igen, a titkos kulcs exportálását választom
  * Minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges 
* Minden további tulajdonság exportálása

## <a name="upload-encryption-certificate-to-cloud-service"></a>A felhőalapú szolgáltatás titkosítási tanúsítvány feltöltése
Feltöltés a a meglévő tanúsítványt, vagy jön létre. A titkosítási kulcsból álló kulcspárt a PFX-fájlt:

* Adja meg a jelszót a titkos kulcs adatainak védelme

## <a name="update-encryption-certificate-in-service-configuration-file"></a>A szolgáltatás konfigurációs fájljában titkosítási tanúsítvány frissítése
Az ujjlenyomat értékét az alábbi beállítások a konfigurációs fájlban frissítse a tölteni a felhőalapú szolgáltatáshoz a tanúsítvány ujjlenyomata:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Gyakori műveletekhez
* Az SSL-tanúsítvány konfigurálása
* Állítson be ügyféltanúsítványokat

## <a name="find-certificate"></a>Tanúsítvány található
Kövesse az alábbi lépéseket:

1. Futtassa a mmc.exe.
2. Fájl -> beépülő modul hozzáadása/eltávolítása...
3. Válassza ki **tanúsítványok**.
4. Kattintson az **Add** (Hozzáadás) parancsra.
5. Válassza ki a tanúsítványt tároló helyét.
6. Kattintson a **Befejezés** gombra.
7. Kattintson az **OK** gombra.
8. Bontsa ki a **tanúsítványok**.
9. Bontsa ki a tanúsítványt tároló csomópontot.
10. Bontsa ki a tanúsítvány gyermekcsomópontja.
11. Válassza ki a tanúsítványt a listán.

## <a name="export-certificate"></a>Tanúsítvány exportálása
Az a **tanúsítvány exportálása varázslóban**:

1. Kattintson a **Tovább** gombra.
2. Válassza ki **Igen**, majd **a titkos kulcs exportálását választom**.
3. Kattintson a **Tovább** gombra.
4. Válassza ki a kívánt kimeneti fájl formátumát.
5. Ellenőrizze a kívánt beállításokat.
6. Ellenőrizze **jelszó**.
7. Adjon meg egy erős jelszót, és erősítse meg.
8. Kattintson a **Tovább** gombra.
9. Írja be vagy tallózással keresse meg az egy fájlnevet, hol tárolja a tanúsítványt (használja a. PFX-kiterjesztéssel).
10. Kattintson a **Tovább** gombra.
11. Kattintson a **Befejezés** gombra.
12. Kattintson az **OK** gombra.

## <a name="import-certificate"></a>Tanúsítvány importálása
A Tanúsítványimportáló varázslóban:

1. Adja meg a tárolási helyét.
   
   * Válassza ki **aktuális felhasználó** Ha csak az aktuális felhasználó futó folyamatok érik el a szolgáltatást
   * Válassza ki **helyi számítógép** Ha a számítógép egyéb folyamatok érik el a szolgáltatást
2. Kattintson a **Tovább** gombra.
3. Ha importál egy fájlból, erősítse meg a fájl elérési útját.
4. Ha importálni egy. PFX-fájlt:
   1. Adja meg a jelszót a titkos kulcsok védelme
   2. Importálási beállítások megadása
5. Válassza ki a "Hely" tanúsítványok ebben a tárolóban
6. Kattintson a **Browse** (Tallózás) gombra.
7. Jelölje ki a kívánt tárolót.
8. Kattintson a **Befejezés** gombra.
   
   * A megbízható legfelső szintű hitelesítésszolgáltató-tároló választása esetén kattintson **Igen**.
9. Kattintson a **OK** összes párbeszédpanel windows rendszeren.

## <a name="upload-certificate"></a>Tanúsítvány feltöltése
Az a [Azure-portálon](https://portal.azure.com/)

1. Válassza ki **a felhőalapú szolgáltatások**.
2. Válassza ki a felhőszolgáltatást.
3. Kattintson a felső menüben **tanúsítványok**.
4. Kattintson az alsó sáv **feltöltése**.
5. Válassza ki azt a tanúsítványfájlt.
6. Ha a rendszer egy. PFX fájlt, írja be a jelszót a titkos kulcshoz.
7. Ezt követően másolja a tanúsítvány ujjlenyomata az új bejegyzést a listában.

## <a name="other-security-considerations"></a>Egyéb biztonsági szempontok
A jelen dokumentumban ismertetett SSL-beállítások a szolgáltatás és az ügyfelek közötti kommunikáció titkosításához, ha a HTTPS-végpont használatával történik. Ez azért fontos, mert adatbázis-hozzáférési hitelesítő adatok, és más potenciálisan bizalmas információk találhatók a kommunikáció során. Ne feledje azonban, hogy a szolgáltatás továbbra is fennáll belső állapotát, a hitelesítő adatokat, beleértve annak a Microsoft Azure SQL-adatbázis a Microsoft Azure-előfizetéshez tartozó metaadat-tároló megadott belső táblájában. A szolgáltatás konfigurációs fájljában a következő beállítás részeként definiált, hogy az adatbázis (. CSCFG-fájl): 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Ebben az adatbázisban tárolt hitelesítő adatok titkosítása. Azonban célszerű, győződjön meg arról, hogy a szolgáltatás központi telepítések egyaránt webes és feldolgozói szerepkörök vannak mindig naprakészek legyenek, és biztonságos, mint azok egyaránt rendelkezik hozzáféréssel a metaadatokat tároló adatbázis és az tárolt hitelesítő adatok titkosításához és visszafejtéséhez használt tanúsítványt. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

