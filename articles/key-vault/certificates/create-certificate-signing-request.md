---
title: CSR létrehozása és egyesítése Azure Key Vault
description: CSR létrehozása és egyesítése Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: 6d66648680aa14baa53372732df52a6c247a0117
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483763"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>CSR létrehozása és egyesítése Key Vault

Azure Key Vault támogatja a kulcstartóban tetszőleges hitelesítésszolgáltató által kiadott digitális tanúsítvány tárolását. Támogatja a tanúsítvány-aláírási kérelem nyilvános kulcspár használatával történő létrehozását, amelyet bármelyik kiválasztott hitelesítésszolgáltató aláírhat. Ez lehet belső vállalati HITELESÍTÉSSZOLGÁLTATÓ vagy külső nyilvános HITELESÍTÉSSZOLGÁLTATÓ. A tanúsítvány-aláírási kérelem (a CSR vagy a minősítési kérelem) egy olyan üzenet, amelyet a felhasználó a hitelesítésszolgáltató (CA) számára küld a digitális tanúsítvány kiállításának kérelmezése érdekében.

A tanúsítványokkal kapcsolatos további általános információkért lásd: [Azure Key Vault tanúsítványok](./about-certificates.md).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="adding-certificate-in-key-vault-issued-by-partnered-ca"></a>Tanúsítvány hozzáadása a partneri HITELESÍTÉSSZOLGÁLTATÓ által kiadott Key Vault
Key Vault partnerek a következő két hitelesítésszolgáltatóval egyszerűsítik a tanúsítványok létrehozását. 

|Szolgáltató|Tanúsítvány típusa|Konfiguráció beállítása  
|--------------|----------------------|------------------|  
|DigiCert|A Key Vault OV vagy EV SSL-tanúsítványokat kínál a DigiCert| [Integrációs útmutató](./how-to-integrate-certificate-authority.md)
|GlobalSign|A Key Vault OV vagy EV SSL-tanúsítványokat kínál a GlobalSign| [Integrációs útmutató](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="adding-certificate-in-key-vault-issued-by-non-partnered-ca"></a>Tanúsítvány hozzáadása a nem társ HITELESÍTÉSSZOLGÁLTATÓ által kiadott Key Vaultban

A következő lépések segítséget nyújtanak olyan hitelesítésszolgáltatók létrehozásában, amelyek nem kapcsolódnak Key Vaulthoz (például a GoDaddy nem megbízható Key Vault-HITELESÍTÉSSZOLGÁLTATÓ). 


### <a name="azure-powershell"></a>Azure PowerShell



1. Először **hozza létre a tanúsítvány-házirendet**. Key Vault nem fogja regisztrálni vagy megújítani a tanúsítványt a kiállítótól a felhasználó nevében, mivel az ebben a forgatókönyvben kiválasztott HITELESÍTÉSSZOLGÁLTATÓ nem támogatott, és így a IssuerName ismeretlenre van állítva.

   ```azurepowershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
    
   > [!NOTE]
   > Ha olyan relatív megkülönböztető nevet (RDN) használ, amelynek az értéke vessző (,), használjon aposztrófokat, és a speciális karaktert idézőjelek közé rendezi. Példa: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. Ebben a példában az érték a következőként van `OU` beolvasva: **docs, contoso**. Ez a formátum minden olyan érték esetében működik, amely egy vesszőt tartalmaz.

2. Tanúsítvány- **aláírási kérelem** létrehozása

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. A **hitelesítésszolgáltató által aláírt** CSR-kérelem beszerzése: a `$csr.CertificateSigningRequest` base4-kódolt tanúsítvány-aláírási kérelem a tanúsítványhoz. Ezt a blobot és a kiírást a kiállító tanúsítványkérelem webhelyére is elvégezheti. Ez a lépés a CA-tól a CA-ig változik, a legjobb módszer a HITELESÍTÉSSZOLGÁLTATÓ útmutatásának megkeresése a lépés végrehajtásához. Olyan eszközöket is használhat, mint például a certreq vagy az OpenSSL, hogy beolvassa a tanúsítványkérelem aláírását, és befejezi a tanúsítvány generálásának folyamatát.


4. **Az aláírt kérelem egyesítése** Key Vault a kiállító által a tanúsítványkérelem aláírása után, visszaállíthatja az aláírt tanúsítványt, és egyesítheti azt a Azure Key Vault-ben létrehozott kezdeti magánhálózati kulcspár használatával.

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    A tanúsítványkérelem sikeresen egyesítve lett.

### <a name="azure-portal"></a>Azure Portal

1.  Ha az Ön által választott HITELESÍTÉSSZOLGÁLTATÓhoz szeretne CSR-t előállítani, navigáljon ahhoz a kulcstartóhoz, amelyhez hozzá szeretné adni a tanúsítványt.
2.  A Key Vault tulajdonságok lapon válassza a **tanúsítványok** lehetőséget.
3.  Válassza a **létrehozó/importálás** fület.
4.  A **tanúsítvány létrehozása** képernyőn válassza ki a következő értékeket:
    - **Tanúsítvány létrehozásának módszere:** Létrehoz.
    - **Tanúsítvány neve:** ContosoManualCSRCertificate.
    - **A hitelesítésszolgáltató típusa (CA):** Nem integrált HITELESÍTÉSSZOLGÁLTATÓ által kiállított tanúsítvány
    - **Tárgy:**`"CN=www.contosoHRApp.com"`
    - Válassza ki a többi értéket a kívánt módon. Kattintson a **Létrehozás** gombra.

    ![Tanúsítvány tulajdonságai](../media/certificates/create-csr-merge-csr/create-certificate.png)  


6.  Látni fogja, hogy a tanúsítvány már hozzá lett adva a tanúsítványok listájában. Válassza ki az imént létrehozott új tanúsítványt. A tanúsítvány aktuális állapota "Letiltva" lesz, mivel még nem állította ki a HITELESÍTÉSSZOLGÁLTATÓ.
7. Kattintson a **tanúsítvány művelete** fülre, és válassza a **CSR letöltése** lehetőséget.

   ![Képernyőfelvétel: a CSR letöltése gomb.](../media/certificates/create-csr-merge-csr/download-csr.png)
 
8.  Írja be a. CSR fájlt a HITELESÍTÉSSZOLGÁLTATÓhoz az aláírásra vonatkozó kéréshez.
9.  Miután a HITELESÍTÉSSZOLGÁLTATÓ aláírja a kérést, hozza vissza a tanúsítványfájl-t az **aláírt kérelem egyesítéséhez** ugyanabban a tanúsítvány-művelet képernyőn.

A tanúsítványkérelem sikeresen egyesítve lett.

> [!NOTE]
> Ha a RDN-értékek vesszővel vannak elválasztva, akkor a **Tárgy** mezőben a 4. lépésben látható idézőjelek értékének megadásával is hozzáadhatja őket.
> Példa a "tárgy" kifejezésre: ebben `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com` a példában a RDN `OU` olyan értéket tartalmaz, amely a névben vessző szerepel. Az eredményül kapott kimenet a `OU` **docs, contoso**.


## <a name="adding-more-information-to-csr"></a>További információk hozzáadása a CSR-hez

Ha további információt szeretne felvenni a CSR létrehozásakor, például: 
    - Ország:
    - Város/helység:
    - Állam/megye:
    - Szervezete
    - Szervezeti egység: az subjectName-ben való definiálásával minden információt hozzáadhat a CSR létrehozásához.

Példa
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

> [!NOTE]
> Ha DV-tanúsítványt kér a CSR összes adatával, a HITELESÍTÉSSZOLGÁLTATÓ elutasítja a kérelmet, mert előfordulhat, hogy nem tudja érvényesíteni a kérelemben szereplő összes információt. Ha egy OV-tanúsítványt kér, megfelelőbb lenne a CSR-ben lévő összes információ hozzáadása.


## <a name="troubleshoot"></a>Hibaelhárítás

- **Hiba típusa: "a megadott X. 509 tanúsítvány tartalmában található végfelhasználói tanúsítvány nyilvános kulcsa nem egyezik a megadott titkos kulcs nyilvános részével. Ellenőrizze, hogy érvényes** -e a tanúsítvány. Ez a hiba akkor fordulhat elő, ha nem egyesíti a CSR-t ugyanazzal a CSR-kéréssel. CSR létrehozásakor mindig létrejön egy titkos kulcs, amelynek az aláírt kérelem egyesítésekor egyeznie kell.
    
- Ha a CSR egyesítve lett, a teljes láncot egyesíteni fogja?
    Igen, a teljes lánc egyesítése történik, ha a felhasználó visszaállította a p7b-fájlt az egyesítéshez.

- Ha a kiállított tanúsítvány "letiltott" állapotban van a Azure Portalban, folytassa a **tanúsítvány művelet** megtekintésével, hogy áttekintse a tanúsítványhoz tartozó hibaüzenetet.

További információkért tekintse meg a [tanúsítványok műveleteit a Key Vault REST API-referenciában](/rest/api/keyvault). Az engedélyek létrehozásával kapcsolatos információkért lásd: tárolók [– Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és tárolók [– frissítési hozzáférési szabályzat](/rest/api/keyvault/vaults/updateaccesspolicy).

- **Hiba típusa: a megadott tulajdonos neve nem érvényes X500-név** Ez a hiba akkor fordulhat elő, ha a SubjectName értékeiben a "speciális karakterek" szerepel. Lásd: megjegyzések Azure Portal és PowerShell-utasításokban. 

## <a name="next-steps"></a>További lépések

- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
