---
title: A Azure Key Vault újdonságai
description: Azure Key Vault legújabb frissítései
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 194b0122987d4fdc5d100112c60006588d28f96c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826918"
---
# <a name="whats-new-for-azure-key-vault"></a>A Azure Key Vault újdonságai

A Azure Key Vault újdonságai. Az új funkciók és Újdonságok az [Azure updates Key Vault csatornán](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)is bejelentve lesznek.

## <a name="july-2020"></a>2020. július

> [!WARNING]
> Ez a két frissítés lehetséges hatással van Azure Key Vault megvalósításokra.

### <a name="soft-delete-on-by-default"></a>Alapértelmezés szerint nem kötelező törlés

A 2020 végére a **Soft-delete alapértelmezés szerint be lesz kapcsolva minden kulcstartónál, amely**új és már meglévő is. Az esetlegesen feltörhető változásokról, valamint az érintett kulcstartók megkeresésének lépéseiről és az azok előzetes frissítéséről a következő cikkben talál további információt: a [Soft-delete minden kulcstartón engedélyezve lesz](soft-delete-change.md). 

### <a name="azure-tls-certificate-changes"></a>Az Azure TLS-tanúsítvány változásai  

A Microsoft frissíti az Azure-szolgáltatásokat, hogy TLS-tanúsítványokat használjanak egy másik legfelső szintű hitelesítésszolgáltatóktól (CA). Ez a változás azért történik, mert az aktuális HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok [nem felelnek meg a hitelesítésszolgáltató/böngésző alapkonfigurációjának egyik követelményének](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951).

### <a name="when-will-this-change-happen"></a>Mikor fog történni ez a változás?

- A [Azure Active Directory](/azure/active-directory) (Azure ad) szolgáltatások a 2020. július 7-én kezdték el ezt az áttérést.
- Minden újonnan létrehozott Azure TLS/SSL-végpont frissített tanúsítványokat tartalmaz az új legfelső szintű hitelesítésszolgáltatókhoz.
- A meglévő Azure-végpontok fokozatosan, 2020-ig, a 2020-es és a-es időszakra kezdődően, fokozatos módon alakulnak.
- Az [Azure IoT hub](https://azure.microsoft.com/services/iot-hub) és a [DPS](/azure/iot-dps/) továbbra is a Baltimore CYBERTRUST legfelső szintű hitelesítésszolgáltatóján marad, de a köztes hitelesítésszolgáltatók is megváltoznak. A részletekért tekintse meg a következő témakört: blob post [Azure IOT TLS: változások jönnek! (... és miért érdeklik)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- Az [Azure Storage](/azure/storage) továbbra is a Baltimore CyberTrust legfelső szintű hitelesítésszolgáltatóján marad, de a köztes hitelesítésszolgáltatók is megváltoznak. A részletekért tekintse meg a blob post [Azure Storage TLS-t: változások jönnek! (... és miért érdeklik)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).

> [!IMPORTANT]
> Előfordulhat, hogy az ügyfeleknek frissíteniük kell az alkalmazás (oka) t a módosítás után, hogy meggátolják a kapcsolódási hibákat az Azure-szolgáltatásokhoz való kapcsolódási kísérlet során.

### <a name="what-is-changing"></a>Mi változik?

Manapság az Azure-szolgáltatások által használt TLS-tanúsítványok többsége a következő legfelső szintű HITELESÍTÉSSZOLGÁLTATÓhoz kapcsolódik:

| A HITELESÍTÉSSZOLGÁLTATÓ köznapi neve | Ujjlenyomat (SHA1) |
|--|--|
| [Baltimore CyberTrust-gyökér](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Az Azure-szolgáltatások által használt TLS-tanúsítványok a következő legfelső szintű hitelesítésszolgáltatók egyikére lesznek felfűzve:

| A HITELESÍTÉSSZOLGÁLTATÓ köznapi neve | Ujjlenyomat (SHA1) |
|--|--|
| [DigiCert globális root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert globális legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust-gyökér](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA legfelső szintű hitelesítésszolgáltató 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft EV ECC legfelső szintű hitelesítésszolgáltató 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Mikor lehet kivonni a régi közbenső ujjlenyomatot?

Az aktuális HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat a rendszer a 2021. február 15-ig *nem* vonja vissza. Ezen időpont után eltávolíthatja a régi ujjlenyomatai megfelelnek a kódból.

Ha ez a dátum változik, a rendszer értesítést küld az új visszavonási dátumról.

### <a name="will-this-change-affect-me"></a>Hatással lesz a változás? 

Várható, hogy **a legtöbb Azure-ügyfél nem** lesz hatással.  Előfordulhat azonban, hogy az alkalmazása érintett, ha explicit módon megadja az elfogadható hitelesítésszolgáltatók listáját. Ezt a gyakorlatot tanúsítvány-rögzítésnek nevezzük.

Az alábbi módokon derítheti fel, hogy az alkalmazás érintett-e:

- Keresse meg az [itt](https://www.microsoft.com/pki/mscorp/cps/default.htm)található Microsoft IT TLS-hitelesítésszolgáltatók ujjlenyomatát, köznapi nevét és egyéb tanúsítvány-tulajdonságait. Ha egyezés van, akkor az alkalmazás hatással lesz rá. A probléma megoldásához frissítse a forráskódot az új hitelesítésszolgáltatók közé. Ajánlott eljárásként győződjön meg arról, hogy a CAs-t rövid felmondással lehet felvenni vagy szerkeszteni. Az iparági szabályozások megkövetelik, hogy a CA-tanúsítványokat hét napon belül le lehessen cserélni, ezért a rögzítésre szoruló ügyfeleknek gyors reagálásra van szükségük.

- Ha olyan alkalmazással rendelkezik, amely integrálható az Azure API-kkal vagy más Azure-szolgáltatásokkal, és nem biztos benne, hogy tanúsítvány-rögzítést használ, ellenőrizze az alkalmazás gyártójával.

- Az Azure-szolgáltatásokkal kommunikáló különböző operációs rendszerek és nyelvi futtatókörnyezetek további lépéseket igényelhetnek a tanúsítványlánc megfelelő felépítéséhez az új gyökerekkel:
    - **Linux**: számos disztribúcióhoz szükséges a CAs hozzáadása a/etc/SSL/certs.-hez A konkrét utasításokért tekintse meg a terjesztés dokumentációját.
    - **Java**: Győződjön meg arról, hogy a Java-kulcs tárolója tartalmazza a fent felsorolt CAs-t.
    - **Leválasztott környezetekben futó Windows**esetén: a leválasztott környezetekben futó rendszereknek hozzá kell adni a megbízható legfelső szintű hitelesítésszolgáltatók tárolójához hozzáadott új gyökereket, valamint a köztes hitelesítésszolgáltatók tárolójához hozzáadott köztes adatkészleteket.
    - **Android**: keresse meg az eszköz és az Android-verzió dokumentációját.
    - **Egyéb hardvereszközök, különösen a IoT**: forduljon az eszköz gyártójához.

- Ha olyan környezettel rendelkezik, amelyben a tűzfalszabályok úgy vannak beállítva, hogy engedélyezze a kimenő hívásokat a visszavont tanúsítványok listájának (CRL) letöltésére és/vagy az online tanúsítványállapot-protokoll (OCSP) ellenőrzési helyeire. A következő CRL-eket és az OCSP URL-címeket is engedélyeznie kell:

    - http://crl3&#46;d igicert&#46;com
    - http://crl4&#46;d igicert&#46;com
    - http://ocsp&#46;d igicert&#46;com
    - http://www&#46;d – megbízhatóság&#46;net
    - http://root-c3-ca2-2009&#46; OCSP&#46;d-Trust&#46;net
    - http://crl&#46; Microsoft&#46;com
    - http://oneocsp&#46; Microsoft&#46;com
    - http://ocsp&#46; msocsp&#46;com

## <a name="june-2020"></a>2020. június

A Key Vault Azure Monitor jelenleg előzetes verzióban érhető el.  A Azure Monitor a Key Vault kérelmek, a teljesítmény, a hibák és a késés egységes áttekintésével biztosítja a kulcstartók átfogó figyelését. További információ: [Azure monitor for Key Vault (előzetes verzió).](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="may-2020"></a>2020. május

A "saját kulcs használata" (BYOK) Key Vault már általánosan elérhető. Tekintse meg a [Azure Key Vault BYOK specifikációját](../keys/byok-specification.md), és Ismerje meg, hogyan [importálhat HSM-védelemmel ellátott kulcsokat a Key Vaultba (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>2020. március

A privát végpontok már előzetes verzióban érhetők el. Az Azure Private link Service lehetővé teszi, hogy a virtuális hálózat privát végpontján keresztül hozzáférhessen Azure Key Vault és az Azure által üzemeltetett ügyfél-és partneri szolgáltatásokhoz.  Ismerje meg, hogyan [integrálhatja Key Vaulteit az Azure Private-hivatkozással](private-link-service.md).

## <a name="2019"></a>2019

- A következő generációs Azure Key Vault SDK-k kiadása. A használatra vonatkozó példákért tekintse meg a [Python](../secrets/quick-create-python.md), a [.net](../secrets/quick-create-net.md), a [Java](../secrets/quick-create-java.md)és a [Node.js](../secrets/quick-create-node.md) Azure Key Vault titkos útmutatóit
- Új Azure-szabályzatok a Key Vault-tanúsítványok kezeléséhez. Tekintse [meg a Key Vault Azure Policy beépített definícióit](../policy-samples.md).
- A Azure Key Vault virtuálisgép-bővítmény már általánosan elérhető.  Lásd: [Key Vault virtuálisgép-bővítmény a Linux](../../virtual-machines/extensions/key-vault-linux.md) rendszerhez és [Key Vault virtuálisgép-bővítmény a Windowshoz](../../virtual-machines/extensions/key-vault-windows.md).
- Az Azure Key Vault-alapú, eseményvezérelt titkok kezelése már elérhető a Azure Event Gridban. További információ: [a Event Grid sémája az eseményekhez a Azure Key Vault] (.. /.. /Event-Grid/Event-Schema-Key-Vault.MD], és megtudhatja, hogyan [fogadhat és reagálhat a Key Vault-értesítésekre Azure Event Grid](event-grid-tutorial.md)használatával.

## <a name="2018"></a>2018

Ebben az évben megjelent új funkciók és integrációk:

- Integráció a Azure Functionssal. A Key Vault-műveletek [Azure Functionsét](../../azure-functions/index.yml) kihasználó példát a [titkos kód elforgatásának automatizálása](../secrets/tutorial-rotation.md)című témakörben talál. 
- [Integráció a Azure Databrickssal](/azure/databricks/scenarios/store-secrets-azure-key-vault). Ezzel a Azure Databricks mostantól két típusú titkos hatókört támogat: a Azure Key Vault-alapú és a Databricks-alapú biztonsági mentést. További információ: [Azure Key Vault-alapú titkos kulcs hatókörének létrehozása](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Azure Key Vault virtuális hálózati szolgáltatási végpontok](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Az idei évben megjelent új funkciók:

- Felügyelt Storage-fiókok kulcsai. A Storage-fiók kulcsainak funkció az Azure Storage-nal való egyszerűbb integrációt teszi ki. További információért tekintse meg az Áttekintés témakört, amely a [felügyelt Storage-fiókok kulcsairól nyújt áttekintést](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Helyreállítható törlés. A Soft-delete funkció javítja a kulcstartók és a Key Vault-objektumok adatvédelmi védelmét. További információért lásd az áttekintést [ismertető témakört.](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

## <a name="2015"></a>2015

Az idei évben megjelent új funkciók:
- Tanúsítványkezelés. Szolgáltatásként hozzáadva a GA 2015-06-01-es verziójához, 2016. szeptember 26-án.

Az általános elérhetőség (2015-06-01-es verzió) a 2015. június 24-én jelent meg. Ebben a kiadásban a következő módosítások történtek: 
- Törölje a "use" mezőt az eltávolításhoz.
- Adatok lekérése a kulcsról – "használat" mező eltávolítva.
- Kulcs importálása egy tárolóba – "használat" mező eltávolítva.
- Egy kulcs visszaállítása – "használat" mező eltávolítva.     
- A "RSA_OAEP" elem "RSA-OAEP" értékre módosult RSA-algoritmusokhoz. Lásd: [a kulcsok, a titkok és a tanúsítványok ismertetése](about-keys-secrets-certificates.md).    
 
A második előzetes verzió (2015-02-01-es verzió – előzetes verzió) bejelentve: 2015. április 20. További információ: [REST API Update](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) blog post. A következő feladatok frissültek:
 
- Egy tár által hozzáadott többoldalas támogatásban lévő kulcsok listázása a művelethez.
- Egy kulccsal felvett művelet verzióinak listázása a kulcsok verzióinak listázásához.  
- Egy tár által hozzáadott tördelési támogatással rendelkező titkok listázása.
- Egy titkos hozzáadási művelet verzióinak listázása egy titkos kulcs verzióinak listázásához.  
- Minden művelet – új létrehozott/frissített időbélyegek az attribútumokhoz.  
- Hozzon létre egy titkos tartalmú tartalmat a titkokhoz.
- Hozzon létre egy kulccsal hozzáadott címkéket opcionális információként.
- Hozzon létre egy titkos címkével kiegészített címkéket opcionális információként.
- Kulcshoz hozzáadott címkék frissítése opcionális információként.
- Titkos címkék frissítése opcionális információként.
- A titkok maximális mérete 10 K és 25 K közötti értékre módosult. Lásd: [kulcsok, titkos kódok és tanúsítványok](about-keys-secrets-certificates.md).    

## <a name="2014"></a>2014
 
Az első előzetes verzió (2014-12-08-es verzió – előzetes verzió) a 2015. január 8-án jelent meg.  

## <a name="next-steps"></a>További lépések

Ha további kérdése van, vegye fel velünk a kapcsolatot az [ügyfélszolgálaton](https://azure.microsoft.com/support/options/)keresztül.  
