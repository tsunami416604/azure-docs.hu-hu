---
title: Biztonsági konfiguráció felosztása
description: X409-es tanúsítványok beállítása titkosításhoz a split/merge szolgáltatással a rugalmas méretezéshez.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: a916645f153f73a98e7fc5d4046bdf557e8acf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823526"
---
# <a name="split-merge-security-configuration"></a>Biztonsági konfiguráció felosztása

A Split/Merge szolgáltatás használatához helyesen kell konfigurálnia a biztonságot. A szolgáltatás a Microsoft Azure SQL Database rugalmas méretezése szolgáltatás része. További információ: [Elastic Scale Split and Merge Service tutorial](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Tanúsítványok konfigurálása

A tanúsítványok kétféleképpen vannak konfigurálva. 

1. [Az SSL-tanúsítvány konfigurálása](#to-configure-the-ssl-certificate)
2. [Ügyféltanúsítványok konfigurálása](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Tanúsítványok beszerzése

A tanúsítványok beszerezhetők a nyilvános hitelesítésszolgáltatóktól vagy a [Windows tanúsítványszolgáltatástól.](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx) Ezek a tanúsítványok beszerzésének előnyben részesített módszerei.

Ha ezek a beállítások nem érhetők el, **önaláírt tanúsítványokat**hozhat létre.

## <a name="tools-to-generate-certificates"></a>Tanúsítványok létrehozásához szükséges eszközök

* [makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Az eszközök futtatása

* A Visual Studios fejlesztői parancssorából olvassa el a [Visual Studio parancssora](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    Ha telepítve van, lépjen a következő:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* A WDK letöltése a [Windows 8.1-ről: Készletek és eszközök letöltése](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Az SSL-tanúsítvány konfigurálása

A kommunikáció titkosításához és a kiszolgáló hitelesítéséhez SSL-tanúsítvány szükséges. Válassza ki az alábbi három forgatókönyv közül a legalkalmazhatóbbat, és hajtsa végre az összes lépést:

### <a name="create-a-new-self-signed-certificate"></a>Új önaláírt tanúsítvány létrehozása

1. [Önaláírt tanúsítvány létrehozása](#create-a-self-signed-certificate)
2. [PFX-fájl létrehozása önaláírt SSL-tanúsítványhoz](#create-pfx-file-for-self-signed-ssl-certificate)
3. [SSL-tanúsítvány feltöltése a felhőszolgáltatásba](#upload-ssl-certificate-to-cloud-service)
4. [Ssl-tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-ssl-certificate-in-service-configuration-file)
5. [Import SSL hitelesítésszolgáltató](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Meglévő tanúsítvány használata a tanúsítványtárolóból
1. [Ssl-tanúsítvány exportálása a tanúsítványtárolóból](#export-ssl-certificate-from-certificate-store)
2. [SSL-tanúsítvány feltöltése a felhőszolgáltatásba](#upload-ssl-certificate-to-cloud-service)
3. [Ssl-tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Meglévő tanúsítvány használata PFX-fájlban
1. [SSL-tanúsítvány feltöltése a felhőszolgáltatásba](#upload-ssl-certificate-to-cloud-service)
2. [Ssl-tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Ügyféltanúsítványok konfigurálása
A szolgáltatásra vonatkozó kérelmek hitelesítéséhez ügyféltanúsítványok szükségesek. Válassza ki az alábbi három forgatókönyv közül a legalkalmazhatóbbat, és hajtsa végre az összes lépést:

### <a name="turn-off-client-certificates"></a>Ügyféltanúsítványok kikapcsolása
1. [Az ügyfél tanúsítványalapú hitelesítésének kikapcsolása](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Új, önaláírt ügyféltanúsítványok kiállítása
1. [Önaláírt hitelesítésszolgáltató létrehozása](#create-a-self-signed-certification-authority)
2. [Hitelesítésszolgáltatói tanúsítvány feltöltése a felhőszolgáltatásba](#upload-ca-certificate-to-cloud-service)
3. [Hitelesítésszolgáltatói tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-ca-certificate-in-service-configuration-file)
4. [Ügyféltanúsítványok kiállítása](#issue-client-certificates)
5. [PFX-fájlok létrehozása ügyféltanúsítványokhoz](#create-pfx-files-for-client-certificates)
6. [Ügyféltanúsítvány importálása](#import-client-certificate)
7. [Ügyféltanúsítvány ujjlenyomatának másolása](#copy-client-certificate-thumbprints)
8. [Engedélyezett ügyfelek konfigurálása a szolgáltatás konfigurációs fájljában](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Meglévő ügyféltanúsítványok használata
1. [Hitelesítési alap kulcsának megkeresése](#find-ca-public-key)
2. [Hitelesítésszolgáltatói tanúsítvány feltöltése a felhőszolgáltatásba](#upload-ca-certificate-to-cloud-service)
3. [Hitelesítésszolgáltatói tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-ca-certificate-in-service-configuration-file)
4. [Ügyféltanúsítvány ujjlenyomatának másolása](#copy-client-certificate-thumbprints)
5. [Engedélyezett ügyfelek konfigurálása a szolgáltatás konfigurációs fájljában](#configure-allowed-clients-in-the-service-configuration-file)
6. [Ügyféltanúsítvány-visszavonási ellenőrzés konfigurálása](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
A szolgáltatásvégpontokhoz való hozzáférés az IP-címek meghatározott tartományaira korlátozható.

## <a name="to-configure-encryption-for-the-store"></a>Titkosítás konfigurálása az üzlethez
A metaadat-tárolóban tárolt hitelesítő adatok titkosításához tanúsítvány szükséges. Válassza ki az alábbi három forgatókönyv közül a legalkalmazhatóbbat, és hajtsa végre az összes lépést:

### <a name="use-a-new-self-signed-certificate"></a>Új önaláírt tanúsítvány használata
1. [Önaláírt tanúsítvány létrehozása](#create-a-self-signed-certificate)
2. [PFX-fájl létrehozása önaláírt titkosítási tanúsítványhoz](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Titkosítási tanúsítvány feltöltése a felhőszolgáltatásba](#upload-encryption-certificate-to-cloud-service)
4. [Titkosítási tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Meglévő tanúsítvány használata a tanúsítványtárolóból
1. [Titkosítási tanúsítvány exportálása a tanúsítványtárolóból](#export-encryption-certificate-from-certificate-store)
2. [Titkosítási tanúsítvány feltöltése a felhőszolgáltatásba](#upload-encryption-certificate-to-cloud-service)
3. [Titkosítási tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Meglévő tanúsítvány használata PFX-fájlban
1. [Titkosítási tanúsítvány feltöltése a felhőszolgáltatásba](#upload-encryption-certificate-to-cloud-service)
2. [Titkosítási tanúsítvány frissítése a szolgáltatás konfigurációs fájljában](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Az alapértelmezett konfiguráció
Az alapértelmezett konfiguráció megtagadja a HTTP-végponthoz való összes hozzáférést. Ez az ajánlott beállítás, mivel az ezekre a végpontokra vonatkozó kérelmek bizalmas információkat, például adatbázis-hitelesítő adatokat tartalmazhatnak.
Az alapértelmezett konfiguráció lehetővé teszi a HTTPS-végponthoz való összes hozzáférést. Ez a beállítás tovább korlátozható.

### <a name="changing-the-configuration"></a>A konfiguráció módosítása
A **végpontra**és végpontra vonatkozó hozzáférés-vezérlési szabályok csoportja a ** \<szolgáltatáskonfigurációs fájl EndpointAcls>** szakaszában van konfigurálva.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

A hozzáférés-vezérlési csoport szabályai \<accesscontrol name=""> szolgáltatáskonfigurációs fájl szakaszában vannak konfigurálva. 

A formátumot a Hálózati hozzáférés-vezérlési listák dokumentációja ismerteti.
Ha például csak a 100.100.0.0 és 100.100.255.255 közötti IP-k számára szeretné engedélyezni a HTTPS-végpont elérését, a szabályok a következőképpen néznek ki:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Szolgáltatásmegtagadás megelőzése
A szolgáltatásmegtagadási támadások észlelésére és megelőzésére két különböző mechanizmus támogatott:

* Az egyidejű kérelmek számának korlátozása távoli állomásonként (alapértelmezés szerint kikapcsolva)
* Hozzáférés korlátozása távoli állomásonként (alapértelmezés szerint be)

Ezek az IIS dinamikus IP-biztonságában is dokumentált funkciókon alapulnak. A konfiguráció módosításakor óvakodjon a következő tényezőktől:

* A proxyk és a hálózati címfordítási eszközök viselkedése a távoli állomásadatokon keresztül
* A webes szerepkör bármely erőforrásához intézett minden kérést figyelembe kell venni (például parancsfájlok, képek stb. betöltése)

## <a name="restricting-number-of-concurrent-accesses"></a>Egyidejű hozzáférések számának korlátozása
A viselkedést a következő beállításokkal konfigurálják:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Módosítsa a DynamicIpRestrictionDenyByConcurrentRequests metódust true értékre a védelem engedélyezéséhez.

## <a name="restricting-rate-of-access"></a>A hozzáférés korlátozása
A viselkedést a következő beállításokkal konfigurálják:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Elutasított kérésre adott válasz konfigurálása
A következő beállítás konfigurálja a visszautasított kérésre adott választ:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Az IIS dinamikus IP-biztonságáról az IIS-ben található dokumentációban olvashat más támogatott értékekről.

## <a name="operations-for-configuring-service-certificates"></a>Szolgáltatási tanúsítványok konfigurálásának műveletei
Ez a témakör csak tájékoztató jellegű. Kövesse a következő konfigurációs lépéseket:

* Az SSL-tanúsítvány konfigurálása
* Ügyféltanúsítványok konfigurálása

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása
Végre:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Testreszabás:

* -n a szolgáltatás URL-címével. A helyettesítő karakterek ("CN=*.cloudapp.net") és az alternatív nevek ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") támogatottak.
* -e a tanúsítvány lejárati dátumával Hozzon létre egy erős jelszót, és adja meg, amikor a rendszer kéri.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>PFX-fájl létrehozása önaláírt SSL-tanúsítványhoz
Végre:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Írja be a jelszót, majd exportálja a tanúsítványt az alábbi beállításokkal:

* Igen, a személyes kulcs exportálása
* Az összes bővített tulajdonság exportálása

## <a name="export-ssl-certificate-from-certificate-store"></a>Ssl-tanúsítvány exportálása a tanúsítványtárolóból
* Tanúsítvány keresése
* Kattintson a Műveletek -> Az összes tevékenység -> exportálása...
* Exportálási tanúsítvány egy be. PFX fájl az alábbi lehetőségeket:
  * Igen, a személyes kulcs exportálása
  * Ha lehetséges, az összes tanúsítvány felvétele a tanúsítványláncba *Az összes kiterjesztett tulajdonság exportálása

## <a name="upload-ssl-certificate-to-cloud-service"></a>SSL-tanúsítvány feltöltése a felhőszolgáltatásba
Tanúsítvány feltöltése a meglévő vagy létrehozott. PFX-fájl az SSL kulcspárral:

* Adja meg a személyes kulcs adatait védő jelszót

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Ssl-tanúsítvány frissítése a szolgáltatás konfigurációs fájljában
Frissítse a szolgáltatáskonfigurációs fájlkövetkező beállításának ujjlenyomatértékét a felhőszolgáltatásba feltöltött tanúsítvány ujjlenyomatával:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Ssl-hitelesítésszolgáltató importálása
Kövesse az alábbi lépéseket minden olyan fiókban/gépen, amely kommunikál a szolgáltatással:

* Kattintson duplán a lehetőségre. CER-fájl a Windows Intézőben
* A Tanúsítvány párbeszédpanelen kattintson a Tanúsítvány telepítése...
* Tanúsítvány importálása a megbízható legfelső szintű hitelesítésszolgáltatók tárolójába

## <a name="turn-off-client-certificate-based-authentication"></a>Az ügyféltanúsítvány-alapú hitelesítés kikapcsolása
Csak az ügyféltanúsítvány-alapú hitelesítés támogatott, és letiltása lehetővé teszi a nyilvános hozzáférést a szolgáltatás végpontjaihoz, kivéve, ha más mechanizmusok vannak érvényben (például a Microsoft Azure virtuális hálózat).

A szolgáltatás konfigurációs fájljában módosítsa a hamis beállítási beállításokat a szolgáltatás kikapcsolására:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Ezután másolja ugyanazt az ujjlenyomatot, mint az SSL-tanúsítvány a hitelesítésszolgáltatótanúsítvány-beállításban:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Önaláírt hitelesítésszolgáltató létrehozása
Az alábbi lépések végrehajtásával hozzon létre egy önaláírt tanúsítványt, amely hitelesítésszolgáltatóként működjön:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Testreszabása

* -e a tanúsítvány lejárati dátumával

## <a name="find-ca-public-key"></a>Nyilvános kulcs keresése a hitelesítéshez
Minden ügyféltanúsítványt a szolgáltatás által megbízhatónak adott hitelesítésszolgáltatónak kell kiállítania. Keresse meg a hitelesítésszolgáltató nyilvános kulcsát, amely a hitelesítéshez használni kívánt ügyféltanúsítványokat a felhőszolgáltatásba való feltöltéshez használni fogja.

Ha a nyilvános kulccsal rendelkező fájl nem érhető el, exportálja a tanúsítványtárolóból:

* Tanúsítvány keresése
  * Ugyanannak a hitelesítésszolgáltatónak az ügyféltanúsítvány keresése
* Kattintson duplán a tanúsítványra.
* A Tanúsítvány párbeszédpanelen válassza a Tanúsítványgörbe lapot.
* Kattintson duplán a hitelesítéspárti bejegyzésre a görbén.
* Jegyezzék fel a tanúsítvány tulajdonságait.
* Zárja be a **Tanúsítvány párbeszédpanelt.**
* Tanúsítvány keresése
  * Keresse meg a fent említett hitelesítésszolgáltatót.
* Kattintson a Műveletek -> Az összes tevékenység -> exportálása...
* Exportálási tanúsítvány egy be. CER az alábbi lehetőségekkel:
  * **Nem, ne exportálja a személyes kulcsot**
  * Ha lehetséges, az összes tanúsítványt is felkell tüntetni a tanúsítványláncban.
  * Az összes kiterjesztett tulajdonság exportálása.

## <a name="upload-ca-certificate-to-cloud-service"></a>Hitelesítésszolgáltatói tanúsítvány feltöltése a felhőszolgáltatásba
Tanúsítvány feltöltése a meglévő vagy létrehozott. CER fájlt a hitelesítéshez nyilvános kulccsal.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Hitelesítésszolgáltatói tanúsítvány frissítése a szolgáltatás konfigurációs fájljában
Frissítse a szolgáltatáskonfigurációs fájlkövetkező beállításának ujjlenyomatértékét a felhőszolgáltatásba feltöltött tanúsítvány ujjlenyomatával:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Frissítse a következő beállítás értékét ugyanazzal az ujjlenyomattal:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Ügyféltanúsítványok kiállítása
A szolgáltatás elérésére jogosult minden egyes személynek rendelkeznie kell egy ügyféltanúsítvánnyal, amelyet kizárólagos használatra állítanak ki, és saját erős jelszót kell választania a személyes kulcs védelme érdekében. 

A következő lépéseket ugyanazon a gépen kell végrehajtani, ahol az önaláírt hitelesítésszolgáltatói tanúsítványt létrehozták és tárolták:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Testreszabása:

* -n azonosítóval ahhoz az ügyfélhez, amely ezzel a tanúsítvánnyal lesz hitelesítve
* -e a tanúsítvány lejárati dátumával
* MyID.pvk és MyID.cer egyedi fájlnévvel ehhez az ügyféltanúsítványhoz

Ez a parancs kérni fogja a jelszó létrehozását, majd egyszer történő használatát. Használjon erős jelszót.

## <a name="create-pfx-files-for-client-certificates"></a>PFX-fájlok létrehozása ügyféltanúsítványokhoz
Minden létrehozott ügyféltanúsítványra hajtsa végre a következőket:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Testreszabása:

    MyID.pvk and MyID.cer with the filename for the client certificate

Írja be a jelszót, majd exportálja a tanúsítványt az alábbi beállításokkal:

* Igen, a személyes kulcs exportálása
* Az összes bővített tulajdonság exportálása
* Annak a személynek, akinek ezt a tanúsítványt kiállították, válassza ki az exportjelszót

## <a name="import-client-certificate"></a>Ügyféltanúsítvány importálása
Minden olyan személynek, akinek ügyféltanúsítványt adtak ki, importálnia kell a kulcspárt a szolgáltatással való kommunikációhoz használt gépekbe:

* Kattintson duplán a lehetőségre. PFX-fájl a Windows Intézőben
* Importálja a tanúsítványt a Személyes tárolóba legalább ezzel a beállítással:
  * Az összes bejelölt kiterjesztett tulajdonság felvétele

## <a name="copy-client-certificate-thumbprints"></a>Ügyféltanúsítvány ujjlenyomatának másolása
Minden olyan személynek, akinek ügyféltanúsítványt adtak ki, az alábbi lépéseket kell követnie a tanúsítvány ujjlenyomatának megszerzéséhez, amely hozzáadódik a szolgáltatás konfigurációs fájljához:

* A certmgr.exe futtatása
* A Személyes lap kijelölése
* Kattintson duplán a hitelesítéshez használt ügyféltanúsítványra
* A megnyíló Tanúsítvány párbeszédpanelen válassza a Részletek lapot
* Győződjön meg arról, hogy a Megjelenítés az Összes megjelenítése
* Jelölje ki az Ujjlenyomat nevű mezőt a listában.
* Az ujjlenyomat értékének másolása
  * Nem látható Unicode karakterek törlése az első számjegy előtt
  * Az összes szóköz törlése

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Engedélyezett ügyfelek konfigurálása a szolgáltatás konfigurációs fájljában
Frissítse a következő beállítás értékét a szolgáltatáskonfigurációs fájlban a szolgáltatáshoz hozzáféréssel rendelkező ügyféltanúsítványok ujjlenyomatainak vesszővel elválasztott listájával:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Ügyféltanúsítvány-visszavonási ellenőrzés konfigurálása
Az alapértelmezett beállítás nem ellenőrzi a hitelesítésszolgáltatót az ügyféltanúsítvány-visszavonási állapotról. Ha az ellenőrzések bekapcsolásához, ha az ügyféltanúsítványokat kiállító hitelesítésszolgáltató támogatja az ilyen ellenőrzéseket, módosítsa a következő beállítást az X509RevocationMode ennumeration ban meghatározott értékek egyikével:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>PFX-fájl létrehozása önaláírt titkosítási tanúsítványokhoz
Titkosítási tanúsítvány esetén hajtsa végre a következőt:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Testreszabása:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Írja be a jelszót, majd exportálja a tanúsítványt az alábbi beállításokkal:

* Igen, a személyes kulcs exportálása
* Az összes bővített tulajdonság exportálása
* A tanúsítvány felhőszolgáltatásba való feltöltésekén szüksége lesz a jelszóra.

## <a name="export-encryption-certificate-from-certificate-store"></a>Titkosítási tanúsítvány exportálása a tanúsítványtárolóból
* Tanúsítvány keresése
* Kattintson a Műveletek -> Az összes tevékenység -> exportálása...
* Exportálási tanúsítvány egy be. PFX fájl az alábbi lehetőségeket: 
  * Igen, a személyes kulcs exportálása
  * Lehetőség szerint az összes tanúsítvány felvétele a tanúsítványláncba 
* Az összes bővített tulajdonság exportálása

## <a name="upload-encryption-certificate-to-cloud-service"></a>Titkosítási tanúsítvány feltöltése a felhőszolgáltatásba
Tanúsítvány feltöltése a meglévő vagy létrehozott. PFX fájl a titkosítási kulcspárral:

* Adja meg a személyes kulcs adatait védő jelszót

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Titkosítási tanúsítvány frissítése a szolgáltatás konfigurációs fájljában
Frissítse a szolgáltatáskonfigurációs fájl következő beállításainak ujjlenyomatértékét a felhőszolgáltatásba feltöltött tanúsítvány ujjlenyomatával:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Közös tanúsítványműveletek
* Az SSL-tanúsítvány konfigurálása
* Ügyféltanúsítványok konfigurálása

## <a name="find-certificate"></a>Tanúsítvány keresése
Kövesse az alábbi lépéseket:

1. Futtassa az mmc.exe futtot.
2. Fájl -> Beépülő modul hozzáadása/eltávolítása...
3. Válassza **a Tanúsítványok**lehetőséget.
4. Kattintson a **Hozzáadás** gombra.
5. Válassza ki a tanúsítványtároló helyét.
6. Kattintson a **Befejezés** gombra.
7. Kattintson az **OK** gombra.
8. Bontsa ki **a Tanúsítványok csomópontot.**
9. Bontsa ki a tanúsítványtároló csomópontot.
10. Bontsa ki a Tanúsítvány gyermekcsomópontot.
11. Jelöljön ki egy tanúsítványt a listában.

## <a name="export-certificate"></a>Tanúsítvány exportálása
A **Tanúsítványexportáló varázslóban:**

1. Kattintson a **Tovább** gombra.
2. Válassza az **Igen**lehetőséget, majd **exportálja a személyes kulcsot.**
3. Kattintson a **Tovább** gombra.
4. Válassza ki a kívánt kimeneti fájlformátumot.
5. Ellenőrizze a kívánt beállításokat.
6. Ellenőrizze **a jelszót**.
7. Adjon meg egy erős jelszót, és erősítse meg.
8. Kattintson a **Tovább** gombra.
9. Írja be vagy tallózással keresse meg a tanúsítvány tárolására való fájlnevet (használja a. PFX kiterjesztés).
10. Kattintson a **Tovább** gombra.
11. Kattintson a **Befejezés** gombra.
12. Kattintson az **OK** gombra.

## <a name="import-certificate"></a>Tanúsítvány importálása
A Tanúsítványimportálás varázslóban:

1. Válassza ki az üzlet helyét.
   
   * Válassza az **Aktuális felhasználó** lehetőséget, ha csak az aktuális felhasználó alatt futó folyamatok férnek hozzá a szolgáltatáshoz
   * Válassza a **Helyi számítógép lehetőséget,** ha a számítógép más folyamatai is hozzáférnek a szolgáltatáshoz
2. Kattintson a **Tovább** gombra.
3. Ha fájlból importál, erősítse meg a fájl elérési útját.
4. Ha importál egy . PFX fájl:
   1. Adja meg a személyes kulcsot védő jelszót
   2. Importálási beállítások megadása
5. Válassza az "Hely" tanúsítványok lehetőséget a következő tárolóban
6. Kattintson a **Browse** (Tallózás) gombra.
7. Válassza ki a kívánt tárolót.
8. Kattintson a **Befejezés** gombra.
   
   * Ha a Megbízható legfelső szintű hitelesítésszolgáltató tárolója lett kiválasztva, kattintson az **Igen**gombra.
9. Kattintson az **OK** gombra az összes párbeszédpanelablakon.

## <a name="upload-certificate"></a>Tanúsítvány feltöltése
Az [Azure portalon](https://portal.azure.com/)

1. Válassza a **Felhőszolgáltatások lehetőséget.**
2. Válassza ki a felhőszolgáltatást.
3. A felső menüben kattintson a **Tanúsítványok parancsra.**
4. Az alsó sávon kattintson a **Feltöltés gombra.**
5. Jelölje ki a tanúsítványfájlt.
6. Ha ez egy . PFX fájlt, adja meg a jelszót a személyes kulcs.
7. Miután elkészült, másolja a tanúsítvány ujjlenyomatát a lista új bejegyzéséből.

## <a name="other-security-considerations"></a>Egyéb biztonsági szempontok
A jelen dokumentumban ismertetett SSL-beállítások titkosítják a kommunikációt a szolgáltatás és az ügyfelek között a HTTPS-végpont használata kor. Ez azért fontos, mert az adatbázis-hozzáférés és a potenciálisan más bizalmas információk hitelesítő adatait tartalmazza a kommunikáció. Ne feledje azonban, hogy a szolgáltatás a Belső táblákban megmarad a belső táblákban a Microsoft Azure SQL-adatbázisban, amelyet a Microsoft Azure-előfizetésben megadott a metaadat-tároláshoz. Ez az adatbázis a szolgáltatáskonfigurációs fájlban () a következő beállítás részeként lett definiálva. CSCFG fájl): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Az adatbázisban tárolt hitelesítő adatok titkosítva vannak. Ajánlott eljárásként azonban győződjön meg arról, hogy a szolgáltatástelepítések webes és feldolgozói szerepkörei naprakészek és biztonságosak legyenek, mivel mindketten hozzáférhetnek a metaadat-adatbázishoz és a tárolt hitelesítő adatok titkosításához és visszafejtéséhez használt tanúsítványhoz. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

