---
title: Tudnivalók a Azure Key Vault tanúsítvány megújításáról
description: Tudnivalók a Azure Key Vault tanúsítvány megújításáról
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: beea6f7472d6bc871b4a2df323445b05671a621f
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224560"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>Tudnivalók a Azure Key Vault tanúsítvány megújításáról

A Azure Key Vault lehetővé teszi a hálózat digitális tanúsítványainak üzembe helyezését, kezelését és telepítését, valamint az alkalmazások biztonságos kommunikációjának engedélyezését. A tanúsítványokkal kapcsolatos további általános információkért lásd: [Azure Key Vault tanúsítványok](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

A rövid élettartamú tanúsítvány vagy a tanúsítvány forgási gyakoriságának növelése korlátozza a támadók hatókörét.

## <a name="certificate-expiration-notifications"></a>Tanúsítvány lejárati értesítései
Először vegye fel a tanúsítványt a Key Vaulthoz, hogy értesítést kapjon, ha a tanúsítványok hamarosan lejárnak (például a PowerShell [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0)használatával) Másodszor konfigurálja, ha értesítést szeretne kapni a tanúsítvány lejáratáról. Az élettartam-művelet konfigurálásához [kövesse az alábbi lépéseket](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)

A Key vaultban három tanúsítvány-létrehozási kategória van. Ez az útmutató segít megérteni, hogyan lehet megvalósítani a tanúsítványok megújítását.
-   Integrált HITELESÍTÉSSZOLGÁLTATÓval (DigiCert vagy GlobalSign) létrehozott tanúsítványok
-   Nem integrált HITELESÍTÉSSZOLGÁLTATÓval létrehozott tanúsítványok
-   Önaláírt tanúsítványok

## <a name="renewal-of-integrated-ca-certificate"></a>Az integrált HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megújítása 
Van egy jó hírünk! Az Azure Key Vaultok gondoskodnak a Microsoft megbízható hitelesítésszolgáltatók által kiadott tanúsítványok (például a DigiCert és a GlobalSign) teljes körű karbantartásáról. Ismerje meg, hogyan [integrálhat egy megbízható hitelesítésszolgáltatót a Key vaulttal](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority).

## <a name="renewal-of-non-integrated-ca-certificate"></a>Nem integrált HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megújítása 
Az Azure Key Vault lehetővé teszi a felhasználók számára, hogy bármely HITELESÍTÉSSZOLGÁLTATÓTÓL importálják a tanúsítványokat, így a felhasználók több Azure-erőforrással is integrálhatók, és egyszerűen üzembe helyezést végezhetnek. Ha amiatt aggódik, hogy lejárt a tanúsítvány nyomon követése, vagy rosszabb, hogy a tanúsítvány már lejárt; Ezután Key Vault tud segíteni a naprakészen maradni. A nem integrált HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokhoz a Key Vault lehetővé teszi, hogy a felhasználó a közeljövőben lejáró e-mailes értesítéseket állítsa be. Ezek az értesítések több felhasználó számára is megadhatók.

A tanúsítvány megújítása érdekében fontos tisztában lennie azzal, hogy az Azure Key Vault-tanúsítvány verziószámú objektum. Ha az aktuális verzió lejár, létre kell hoznia egy új verziót. Elméletileg minden új verzió egy olyan új tanúsítvány, amely egy kulcsból és egy olyan blobból áll, amely egy identitáshoz kötődik. Ha nem partneri HITELESÍTÉSSZOLGÁLTATÓT használ, a Key Vault létrehoz egy kulcspár-párt, és visszaküldi a CSR-t.

**Követendő lépések Azure Portal: –**
1.  Nyissa meg a megújítani kívánt tanúsítványt.
2.  Kattintson a **+ új verzió** gombra a tanúsítvány képernyőjén.
3.  **Tanúsítvány kiválasztása művelet**
4.  Válassza a **CSR letöltése**lehetőséget. Ezzel letölti a. CSR fájlt a helyi meghajtón.
5.  Kérje meg a CSR-t a választott HITELESÍTÉSSZOLGÁLTATÓra a kérelem aláírásához
6.  Hozza vissza az aláírt kérést, és válassza a **CSR egyesítése** ugyanazon a tanúsítvány-üzemeltetési képernyőn lehetőséget.

> [!NOTE]
> Fontos, hogy egyesítse az aláírt CSR-t ugyanazzal a CSR-kérelemmel, amelyet létrehoztak, ellenkező esetben a kulcs nem egyezik.

A lépések hasonlóak az új tanúsítvány létrehozásához, és az [itt]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)található részletek részletesebb dokumentálása.

## <a name="renewal-of-self-signed-certificate"></a>Önaláírt tanúsítvány megújítása

Jó hír! Az Azure Key Vaultok emellett az önaláírt tanúsítványok automatikus megújítását is figyelembe veszik a felhasználók számára. Ha többet szeretne megtudni a kiállítási szabályzat módosításáról és a tanúsítvány élettartama műveleti attribútumainak frissítéséről, olvassa el a [További tudnivalókat.](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)

### <a name="troubleshoot"></a>Hibaelhárítás
Ha a kiállított tanúsítvány "letiltott" állapotban van a Azure Portalban, folytassa a tanúsítvány művelet megtekintésével, hogy megtekinthesse a tanúsítványhoz tartozó hibaüzenetet.

### <a name="frequently-asked-questions"></a>Gyakori kérdések
* Hogyan tesztelhető a tanúsítvány autorotációs funkciója?
  Hozzon létre egy hónap érvényességével rendelkező tanúsítványt, majd állítsa be az élettartamot 1%-ban. Ez a beállítás 7,2 órán belül elforgatja a tanúsítványt.
  
* A rendszer replikálja a címkéket a tanúsítvány automatikus megújítása után?
  Nem, a címkék nem replikálódnak, hacsak a felhasználó manuálisan nem másolja magát a címkéket.

### <a name="see-also"></a>Lásd még:
*   [A Key Vault integrálása a DigiCert hitelesítésszolgáltatóval](how-to-integrate-certificate-authority.md)
*   [Oktatóanyag: a tanúsítvány automatikus elforgatásának konfigurálása Key Vaultban](tutorial-rotate-certificates.md)
