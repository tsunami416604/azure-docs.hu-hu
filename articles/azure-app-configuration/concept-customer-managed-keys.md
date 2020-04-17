---
title: Ügyfél által felügyelt kulcsok használata a konfigurációs adatok titkosításához
description: A konfigurációs adatok titkosítása ügyfél által kezelt kulcsokkal
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: ace34cf4a72b871ba6646b279007b8ce21c03e9b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457433"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Az alkalmazáskonfigurációs adatok titkosítása ügyfél által felügyelt kulcsok használatával
Az Azure App Configuration [titkosítja a bizalmas adatokat.](../security/fundamentals/encryption-atrest.md) Az ügyfél által felügyelt kulcsok használata fokozott adatvédelmet biztosít azáltal, hogy lehetővé teszi a titkosítási kulcsok kezelését.  Felügyelt kulcstitkosítás használata esetén az alkalmazáskonfigurációban lévő összes bizalmas információ titkosítva van egy felhasználó által biztosított Azure Key Vault-kulccsal.  Ez lehetővé teszi a titkosítási kulcs igény szerinti elforgatását.  Emellett lehetővé teszi az Azure App Configuration bizalmas adatokhoz való hozzáférésének visszavonását az alkalmazáskonfigurációs példány kulcshoz való hozzáférésének visszavonásával.

## <a name="overview"></a>Áttekintés 
Az Azure App Configuration titkosítja a bizalmas adatokat inaktív egy 256 bites AES titkosítási kulcs a Microsoft által biztosított. Minden alkalmazáskonfigurációs példány saját titkosítási kulccsal rendelkezik a szolgáltatás által kezelt és a bizalmas adatok titkosításához. A bizalmas adatok közé tartoznak a kulcs-érték párokban található értékek.  Ha az ügyfél által felügyelt kulcs képesség engedélyezve van, az Alkalmazás konfigurációja az alkalmazáskonfigurációs példányhoz rendelt felügyelt identitást használja az Azure Active Directoryval való hitelesítéshez. A felügyelt identitás ezután meghívja az Azure Key Vaultot, és becsomagolja az alkalmazáskonfigurációs példány titkosítási kulcsát. A burkolt titkosítási kulcs ezután tárolják, és a kicsomagolatlan titkosítási kulcs gyorsítótárazása az alkalmazás konfigurációjában egy órán keresztül. Az alkalmazáskonfiguráció óránként frissíti az alkalmazáskonfigurációs példány titkosítási kulcsának kicsomagolatlan verzióját. Ez biztosítja a rendelkezésre állást normál üzemi körülmények között. 

>[!IMPORTANT]
> Ha az alkalmazáskonfigurációs példányhoz rendelt identitás már nem jogosult a példány titkosítási kulcsának kicsomagolására, vagy ha a kezelt kulcsot véglegesen törlik, akkor a továbbiakban nem lehet visszafejteni az alkalmazáskonfigurációs példányban tárolt bizalmas adatokat. Az Azure Key Vault [ideiglenes törlési](../key-vault/general/overview-soft-delete.md) funkciójának használatával csökkentheti a titkosítási kulcs véletlen törlésének esélyét.

Ha a felhasználók engedélyezik az ügyfél által felügyelt kulcs funkció az Azure App konfigurációs példányát, ők szabályozzák a szolgáltatás képes hozzáférni a bizalmas adatokat. A kezelt kulcs gyökértitkosítási kulcsként szolgál. A felhasználó visszavonhatja az alkalmazáskonfigurációs példány hozzáférését a felügyelt kulcshoz a kulcstartó hozzáférési szabályzatának módosításával. Ha ezt a hozzáférést visszavonják, az Alkalmazáskonfiguráció egy órán belül elveszíti a felhasználói adatok visszafejtését. Ezen a ponton az alkalmazáskonfigurációs példány minden hozzáférési kísérletet megtilt. Ez a helyzet helyreállítható azáltal, hogy a szolgáltatás ismét hozzáférést biztosít a felügyelt kulcshoz.  Egy órán belül az Alkalmazáskonfiguráció képes lesz visszafejteni a felhasználói adatokat, és normál körülmények között működni.

>[!NOTE]
>Az Azure App konfigurációs összes adata legfeljebb 24 órán keresztül tárolható egy elkülönített biztonsági mentésben. Ez magában foglalja a csomagolatlan titkosítási kulcsot. Ezek az adatok nem érhetők el azonnal a szolgáltatás vagy a szolgáltatási csapat számára. Vészhelyzeti visszaállítás esetén az Azure App Configuration újra visszavonja magát a felügyelt kulcsadatokból.

## <a name="requirements"></a>Követelmények
Az Azure App-konfiguráció hoz az ügyfél által felügyelt kulcs funkció sikeres engedélyezéséhez a következő összetevőkszükségesek:
- Standard szintű Azure App konfigurációs példánya
- Azure Key Vault a helyreállítható törlési és törlésvédelmi funkciók engedélyezésével
- RSA- vagy RSA-HSM-kulcs a Key Vaultban
    - A kulcs nem lehet lejárt, engedélyezni kell, és a wrap és a kicsomagolás i képességeinek is engedélyezve kell lennie.

Az erőforrások konfigurálása után két lépés marad, amely lehetővé teszi, hogy az Azure App Configuration a Key Vault-kulcs használatát használhassa:
1. Felügyelt identitás hozzárendelése az Azure App konfigurációs példányához
2. Adja meg `GET`az `WRAP`identitást és `UNWRAP` az engedélyeket a célkulcstartó hozzáférési szabályzatában.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Ügyfél által felügyelt kulcstitkosítás engedélyezése az Azure App konfigurációs példányához
A kezdéshez szüksége lesz egy megfelelően konfigurált Azure App Konfigurációs példány. Ha még nem rendelkezik elérhető alkalmazáskonfigurációs példánysal, az alábbi rövid útmutatók egyikével állítsa be az alábbi példányokat:
- [Hozzon létre egy ASP.NET-alkalmazást az Azure App Konfigurációjával](quickstart-aspnet-core-app.md)
- [.NET Core alkalmazás létrehozása az Azure App konfigurációjával](quickstart-dotnet-core-app.md)
- [.NET Framework alkalmazás létrehozása az Azure App konfigurációjával](quickstart-dotnet-app.md)
- [Java Spring alkalmazás létrehozása az Azure App Configuration alkalmazáskonfigurációjával](quickstart-java-spring-app.md)

>[!TIP]
> Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj, amely segítségével futtathatja a parancssori utasításokat ebben a cikkben.  Előtelepített azure-eszközökkel rendelkezik, beleértve a .NET Core SDK-t is. Ha be van jelentkezve az Azure-előfizetésbe, indítsa el az [Azure Cloud Shell-t](https://shell.azure.com) shell.azure.com.  Az Azure Cloud Shellről a [dokumentáció elolvasásával](../cloud-shell/overview.md) tudhat meg többet.

### <a name="create-and-configure-an-azure-key-vault"></a>Azure Key Vault létrehozása és konfigurálása
1. Hozzon létre egy Azure Key Vault az Azure CLI használatával.  Vegye figyelembe, hogy mindkettő, `vault-name` és `resource-group-name` a felhasználó által biztosított, és egyedinek kell lennie.  `contoso-vault` Használjuk, `contoso-resource-group` és ezekben a példákban.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Engedélyezze a helyreállítható törlést és a törlési védelmet a Key Vaultszámára. Helyettesítse az`contoso-vault``contoso-resource-group`1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Hozzon létre egy Key Vault-kulcsot. Adjon meg `key-name` egy egyedi t, és helyettesítse`contoso-vault`az 1. Adja meg, `RSA` `RSA-HSM` hogy a titkosítást szeretné-e vagy sem.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    A parancs kimenete a létrehozott kulcs kulcsazonosítóját ("kid") jeleníti meg.  Jegyezze fel a gyakorlat későbbi részében használandó kulcsazonosítót.  A kulcsazonosító formája: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`.  A kulcsazonosító három fontos összetevőből áll:
    1. Key Vault URI: 'https://{my key vault}.vault.azure.net
    1. Key Vault kulcsneve: {Kulcsnév}
    1. Key Vault kulcsverziója: {Key version}

1. Hozzon létre egy rendszer höz rendelt felügyelt identitást az Azure CLI használatával, az alkalmazáskonfigurációs példány és az előző lépésekben használt erőforráscsoport nevének helyettesítésével. A felügyelt identitás a felügyelt kulcs eléréséhez lesz használva. Egy `contoso-app-config` alkalmazáskonfigurációs példány nevének szemléltetésére használjuk:
    
    ```azurecli
    az appconfig identity assign --na1. me contoso-app-config --group contoso-resource-group --identities [system]
    ```
    
    A parancs kimenete tartalmazza a rendszer hozzárendelt identitásának fő azonosítóját ("principalId") és bérlői azonosítóját ("tenandId").  Ezzel biztosíthozzáférést az identitáshoz a felügyelt kulcshoz.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Az Azure App Configuration-példány felügyelt identitásának hozzáférésre van szüksége a kulcshoz a kulcs érvényesítése, titkosítása és visszafejtése elvégzéséhez. A műveletek konkrét készlete, amelyhez `GET` `WRAP`hozzáférésre `UNWRAP` van szüksége, a következőket tartalmazza: , , és a kulcsok esetében.  A hozzáférés megadása megköveteli az alkalmazáskonfigurációs példány felügyelt identitásának elsődleges azonosítóját. Ezt az értéket az előző lépésben kapták meg. Az alábbiakban `contoso-principalId`látható. Adjon engedélyt a felügyelt kulcsnak a következő parancssorból:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Miután az Azure App Konfigurációs példány a felügyelt kulcs eléréséhez, engedélyezheti az ügyfél által felügyelt kulcs képesség a szolgáltatásban az Azure CLI használatával. A legfontosabb létrehozási lépések során `key name` `key vault URI`rögzített következő tulajdonságok visszahívása: .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Az Azure App konfigurációs példánya most úgy van konfigurálva, hogy az Azure Key Vaultban tárolt ügyfél által kezelt kulcsot használjon.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben úgy konfigurálta az Azure App konfigurációs példányát, hogy egy ügyfél által felügyelt kulcsot használjon a titkosításhoz.  Ismerje meg, hogyan [integrálhatja a szolgáltatást az Azure felügyelt identitásaival.](howto-integrate-azure-managed-service-identity.md)