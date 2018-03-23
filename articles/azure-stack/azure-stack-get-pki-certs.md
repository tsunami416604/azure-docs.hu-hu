---
title: Azure verem nyilvános kulcsokra épülő infrastruktúrát tanúsítványok integrált Azure verem rendszerek központi telepítés létrehozása |} Microsoft Docs
description: Az Azure verem PKI-tanúsítvány telepítési folyamatának integrált Azure verem rendszerek ismerteti.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 4a7432260167b8740567ad4308e102417abad82c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>Azure Alkalmazásveremben üzembe nyilvános kulcsú infrastruktúra tanúsítványainak előállításához
Most, hogy tudja [a PKI-tanúsítványkövetelmények](azure-stack-pki-certs.md) Azure verem telepítések esetén be kell szereznie ezeknek a tanúsítványoknak a tanúsítvány hitelesítésszolgáltatói (CA) az Ön által választott. 

## <a name="request-certificates-using-an-inf-file"></a>Tanúsítvány igénylése az INF-fájl használatával
Egy igényel tanúsítványokat nyilvános Hitelesítésszolgáltatótól vagy belső Tanúsítványszolgáltató módja az INF-fájl használatával. A Windows beépített certreq.exe segédprogram az INF-fájl megadása tanúsítvány, segítségével hozható létre olyan kérelem fájl ebben a szakaszban leírtak szerint. 

### <a name="sample-inf-file"></a>Minta INF-fájl 
A példa tanúsítvány kérelem INF-fájl segítségével hozzon létre egy kapcsolat nélküli kérelem tanúsítványfájlt elküldhesse azokat egy Hitelesítésszolgáltatónak (belső vagy nyilvános). Az INF-fájl tartalmazza a szükséges végpontok (beleértve a nem kötelező PaaS-szolgáltatásokat) egyetlen helyettesítő tanúsítvány. 

A minta INF-fájl azt feltételezi, hogy adott régióban egyenlő **tengeri** és a külső FQDN érték **tengeri&#46;contoso&#46;com**. Módosítsa megfelelően a környezet létrehozása előtt ezeket az értékeket egy. Az üzembe helyezéshez INF-fájl. 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>Létrehozni, és küldje el a kérelmet a hitelesítésszolgáltatónak.
Az alábbi munkafolyamat ismerteti, hogyan testre szabhatja és a korábban létrehozott tanúsítvány kérése egy hitelesítésszolgáltatótól minta INF-fájlját használja:

1. **Szerkesztheti és mentheti INF-fájl**. A minta másolása a megadott, és egy új szöveges fájlt mentse. A tulajdonos neve és a külső teljes Tartománynevet cserélje le az értékeket, amelyeket felel meg a központi telepítés, és mentse a fájlt egy. INF-fájl.
2. **Létre a certreq használatával**. Windows számítógépet használ, elindítani a parancssort rendszergazdaként, és hozza létre a kérelem (.req) fájlt a következő parancsot: `certreq -new <yourinffile>.inf <yourreqfilename>.req`.
3. **Hitelesítésszolgáltató terjeszt**. Küldje el a. KÉRÉS fájl jön létre a hitelesítésszolgáltatóhoz (lehet belső vagy nyilvános).
4. **Importálja. CER**. A CA értéket ad vissza egy. CER-fájljával. Az azonos Windows-számítógép, amelyből létre a kérelem fájl segítségével importálhatja a. A számítógép, illetve személyes tárolójába visszaadott CER-fájljával. 
5. **Exportálni, és másolja. A telepítési mappák PFX**. (A titkos kulcsot is beleértve) a tanúsítvány exportálása a. PFX fájlt, és másolja a. PFX-fájlt a telepítési mappák ismertetett [Azure verem PKI követelményektől](azure-stack-pki-certs.md).

## <a name="next-steps"></a>További lépések
[Azure verem PKI-tanúsítványok előkészítése](prepare-pki-certs.md)