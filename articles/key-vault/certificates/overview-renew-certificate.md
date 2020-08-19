---
title: Tudnivalók a Azure Key Vault tanúsítvány megújításáról
description: Ez a cikk a tanúsítványok Azure Key Vaultának megújítását ismerteti.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 3809fa9e1ce17a5a0c3cf333ac20ef543db4b5a7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588803"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Azure Key Vault-tanúsítványok megújítása

A Azure Key Vault segítségével könnyedén kiépítheti, kezelheti és telepítheti a hálózat digitális tanúsítványait, és engedélyezheti az alkalmazásai számára a biztonságos kommunikációt. További információ a tanúsítványokról: [Tudnivalók a Azure Key Vault tanúsítványokról](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates).

A rövid élettartamú tanúsítványok vagy a tanúsítvány-elforgatás gyakoriságának növelésével megakadályozhatja, hogy jogosulatlan felhasználók hozzáférjenek az alkalmazásaihoz.

Ez a cikk a Azure Key Vault-tanúsítványok megújítását ismerteti.

## <a name="get-notified-about-certificate-expirations"></a>Értesítés a tanúsítvány lejáratáról
Ha értesítést szeretne kapni, ha a tanúsítványok hamarosan lejárnak, tegye a következőket:

Először adjon hozzá egy tanúsítványt a Key vaulthoz a [Add-AzureKeyVaultCertificateContact PowerShell-](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0)parancsmag használatával.

Másodszor, konfigurálja, ha értesítést szeretne kapni a tanúsítvány lejáratáról. A tanúsítvány életciklus-attribútumainak konfigurálásához tekintse [meg a tanúsítvány autorotációjának konfigurálása Key Vaultban](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)című témakört.

Key Vault a tanúsítványok három kategóriába sorolhatók:
-   Integrált hitelesítésszolgáltatóval (CA) létrehozott tanúsítványok, például DigiCert vagy GlobalSign
-   Nem integrált HITELESÍTÉSSZOLGÁLTATÓval létrehozott tanúsítványok
-   Önaláírt tanúsítványok

## <a name="renew-an-integrated-ca-certificate"></a>Integrált HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megújítása 
Azure Key Vault kezeli a megbízható Microsoft-hitelesítésszolgáltatók DigiCert és GlobalSign által kiállított tanúsítványok végpontok közötti karbantartását. Ismerje meg, hogyan [integrálhat egy megbízható hitelesítésszolgáltatót Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)használatával.

## <a name="renew-a-nonintegrated-ca-certificate"></a>Nem integrált HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megújítása 
A Azure Key Vault használatával bármilyen HITELESÍTÉSSZOLGÁLTATÓTÓL importálhat tanúsítványokat, így számos Azure-erőforrással integrálható, és egyszerűen üzembe helyezhető. Ha aggódik a tanúsítvány lejárati dátumának elvesztésével kapcsolatban, vagy ha még rosszabb, felfedezte, hogy egy tanúsítvány már lejárt, a kulcstartó segíthet a naprakészen tartásban. A nem integrált HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok esetében a Key Vault lehetővé teszi, hogy közel lejáratú e-mailes értesítéseket hozzon létre. Ilyen értesítések több felhasználó számára is megadhatók.

> [!IMPORTANT]
> A tanúsítvány verziószámú objektum. Ha az aktuális verzió lejár, létre kell hoznia egy új verziót. Elméletileg minden új verzió egy olyan új tanúsítvány, amely egy kulcsból és egy olyan blobból áll, amely egy identitáshoz kötődik. Ha nem partneri HITELESÍTÉSSZOLGÁLTATÓT használ, a Key Vault létrehoz egy kulcs/érték párokat, és visszaadja a tanúsítvány-aláírási kérelmet (CSR).

Nem integrált HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megújításához tegye a következőket:

1. Jelentkezzen be a Azure Portalba, majd nyissa meg a megújítani kívánt tanúsítványt.
1. A tanúsítvány ablaktáblán válassza az **új verzió**elemet.
1. Válassza a **tanúsítvány művelet**lehetőséget.
1. Válassza a **CSR letöltése** lehetőséget, hogy letöltse a CSR-fájlt a helyi meghajtóra.
1. Küldje el az CSR-t a választott HITELESÍTÉSSZOLGÁLTATÓnak a kérelem aláírásához.
1. Hozza vissza az aláírt kérelmet, és válassza a **CSR egyesítése** elemet ugyanazon a tanúsítvány műveleti ablaktáblán.

> [!NOTE]
> Fontos, hogy egyesítse az aláírt CSR-t ugyanazzal a CSR-kéréssel, amelyet Ön hozott létre. Ellenkező esetben a kulcs nem egyezik.

Az új CSR létrehozásával kapcsolatos további információkért lásd: [CSR létrehozása és egyesítése Key Vaultban]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renew-a-self-signed-certificate"></a>Önaláírt tanúsítvány megújítása

A Azure Key Vault az önaláírt tanúsítványok automatikus megújítását is kezeli. Ha többet szeretne megtudni a kiállítási szabályzat módosításáról és a tanúsítvány életciklus-attribútumainak frissítéséről, tekintse meg a [tanúsítvány autorotációjának konfigurálása Key Vaultban](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)című témakört.

## <a name="troubleshoot"></a>Hibaelhárítás
Ha a kiállított tanúsítvány *letiltott* állapotban van a Azure Portalban, lépjen a **tanúsítvány műveletre** a tanúsítvány hibaüzenetének megtekintéséhez.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Hogyan tesztelhető a tanúsítvány autorotációs funkciója?**

Hozzon létre egy **hónap**érvényességével rendelkező tanúsítványt, majd állítsa be az élettartam műveletet az elforgatáshoz **1%**-ban. Ez a beállítás 7,2 óránként elforgatja a tanúsítványt.
  
**A rendszer replikálja a címkéket a tanúsítvány automatikus megújítása után?**

Igen, a címkék az automatikus megújítás után replikálódnak.

## <a name="next-steps"></a>További lépések
*   [Key Vault integrálása DigiCert-hitelesítésszolgáltatóval](how-to-integrate-certificate-authority.md)
*   [Oktatóanyag: a tanúsítvány autorotációjának konfigurálása Key Vaultban](tutorial-rotate-certificates.md)
