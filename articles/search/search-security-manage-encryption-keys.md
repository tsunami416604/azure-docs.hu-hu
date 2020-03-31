---
title: Inaktív titkosítás az ügyfél által felügyelt kulcsok használatával
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search ben az indexek és szinonima-leképezések kiszolgálóoldali titkosítást az Azure Key Vaultban létrehozott és felügyelt kulcsok használatával egészítse ki.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899947"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Tartalom titkosítása az Azure Cognitive Search szolgáltatásban az ügyfelek által felügyelt kulcsok használatával az Azure Key Vaultban

Alapértelmezés szerint az Azure Cognitive Search szolgáltatás által felügyelt kulcsokkal titkosítja az inaktív indexelt [tartalmat.](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models) Az alapértelmezett titkosítást kiegészítheti egy további titkosítási réteggel az Azure Key Vaultban létrehozott és felügyelt kulcsok használatával. Ez a cikk végigvezeti a lépéseken.

A kiszolgálóoldali titkosítást az [Azure Key Vaultszolgáltatással](https://docs.microsoft.com/azure/key-vault/key-vault-overview)való integráció támogatja. Létrehozhat saját titkosítási kulcsokat, és tárolhatja őket egy key vaultban, vagy használhatja az Azure Key Vault API-kat titkosítási kulcsok létrehozásához. Az Azure Key Vault segítségével is naplózhatja a kulcshasználatot. 

Az ügyfél által felügyelt kulcsokkal történő titkosítás az index vagy a szinonimaleképezés szintjén van konfigurálva, amikor ezeket az objektumokat létrehozják, és nem a keresési szolgáltatás szintjén. A már létező tartalom nem titkosítható. 

A kulcsoknak nem kell ugyanabban a key vaultban lenniük. Egyetlen keresési szolgáltatás több titkosított indexet vagy szinonimát is üzemeltethet, amelyek mindegyike a különböző kulcstárolókban tárolt saját, ügyfél által felügyelt titkosítási kulcsokkal van titkosítva.  Indexeket és szinonimát is használhat ugyanabban a szolgáltatásban, amelyek nem titkosítottak az ügyfél által felügyelt kulcsok használatával. 

> [!IMPORTANT] 
> Ez a funkció a [REST API 2019-05-06-os](https://docs.microsoft.com/rest/api/searchservice/) verziójában és [a .NET SDK 8.0-preview verziójában](search-dotnet-sdk-migration-version-9.md)érhető el. Jelenleg nincs támogatás az ügyfél által felügyelt titkosítási kulcsok konfigurálásához az Azure Portalon. A keresési szolgáltatást 2019 januárja után kell létrehozni, és nem lehet ingyenes (megosztott) szolgáltatás.

## <a name="prerequisites"></a>Előfeltételek

Ebben a példában a következő szolgáltatásokat használjuk. 

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. 

+ [Hozzon létre egy Azure Key Vault-erőforrást,](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) vagy keressen egy meglévő tárolót az előfizetése alatt.

+ [Az Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) konfigurációs feladatokhoz használatos.

+ [Postman](search-get-started-postman.md), [Az Azure PowerShell](search-create-index-rest-api.md) és [az Azure Cognitive Search SDK](https://aka.ms/search-sdk-preview) a REST API-hívásához használható. Jelenleg nincs portáltámogatás az ügyfél által felügyelt titkosításhoz.

>[!Note]
> Az ügyfél által felügyelt kulcsok funkcióval való titkosítás jellege miatt az Azure Cognitive Search nem fogja tudni lekérni az adatokat, ha az Azure Key Vault-kulcs törlődik. A Key Vault véletlen kulcstörlései által okozott adatvesztés elkerülése érdekében engedélyeznie **kell** a Kulcstartóban a helyreállítható törlés és a kiürítési védelem védelmét, mielőtt használható lenne. További információ: [Azure Key Vault soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="1---enable-key-recovery"></a>1 - Kulcshelyreállítás engedélyezése

Az Azure Key Vault-erőforrás létrehozása után engedélyezze a **rendszerideiglenes törlésés** **a védelem kiürítése szolgáltatást** a kiválasztott kulcstárolóban a következő PowerShell- vagy Azure CLI-parancsok végrehajtásával:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - Új kulcs létrehozása

Ha egy meglévő kulcsot használ az Azure Cognitive Search-tartalom titkosításához, hagyja ki ezt a lépést.

1. [Jelentkezzen be az Azure Portalon,](https://portal.azure.com) és keresse meg a key vault irányítópultját.

1. A bal oldali navigációs ablakban jelölje ki a **Kulcsok** beállítást, majd kattintson **a + Létrehozás/importálás gombra.**

1. A **Kulcs létrehozása** ablaktáblán a **Beállítások**listából válassza ki a kulcs létrehozásához használni kívánt módszert. **Létrehozhat** egy új kulcsot, **feltölthet** egy meglévő kulcsot, vagy a **Biztonsági másolat visszaállítása** segítségével kiválaszthat egy biztonsági másolatot.

1. Adja meg a kulcs **nevét,** és tetszés szerint jelöljön ki más kulcstulajdonságokat.

1. Kattintson a **Létrehozás** gombra a telepítés elindításához.

Jegyezze fel a kulcsazonosítót – ez az **Uri kulcsértékből**, a **kulcsnévből**és a **kulcsverzióból**áll. Ezekre szüksége lesz egy titkosított index definiálásához az Azure Cognitive Search-ben.
 
![Új kulcstartó létrehozása](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Új kulcstartó létrehozása")

## <a name="3---create-a-service-identity"></a>3 - Szolgáltatásidentitás létrehozása

Identitás hozzárendelése a keresési szolgáltatás lehetővé teszi, hogy key vault hozzáférési engedélyeket a keresési szolgáltatás. A keresési szolgáltatás az identitását használja az Azure Key Vault használatával történő hitelesítéshez.

Az Azure Cognitive Search kétféle módon támogatja az identitások hozzárendelését: egy felügyelt identitást vagy egy külsőleg felügyelt Azure Active Directory-alkalmazást. 

Ha lehetséges, használjon felügyelt identitást. Ez a legegyszerűbb módja annak, hogy identitást rendel a keresési szolgáltatáshoz, és a legtöbb esetben működnie kell. Ha több kulcsot használ az indexek és szinonima leképezések, vagy ha a megoldás egy elosztott architektúra, amely kizárja az identitásalapú hitelesítés, használja a speciális [külsőleg felügyelt Azure Active Directory-megközelítés](#aad-app) a cikk végén leírt.

 A felügyelt identitás általában lehetővé teszi, hogy a keresési szolgáltatás hitelesítse magát az Azure Key Vault hitelesítő adatok kódban való tárolása nélkül. Az ilyen típusú felügyelt identitás életciklusa a keresési szolgáltatás életciklusához van kötve, amely csak egy felügyelt identitással rendelkezhet. [További információ a felügyelt identitásokról.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

1. Felügyelt identitás létrehozásához [jelentkezzen be az Azure-portálra,](https://portal.azure.com) és nyissa meg a keresési szolgáltatás irányítópultját. 

1. Kattintson az **Identitás gombra** a bal oldali navigációs ablakban, módosítsa az állapotát **Be**állásra, majd kattintson a **Mentés gombra.**

![Felügyelt identitás engedélyezése](./media/search-enable-msi/enable-identity-portal.png "Manged identitás engedélyezése")

## <a name="4---grant-key-access-permissions"></a>4 - Kulcshozzáférési engedélyek megadása

Ahhoz, hogy a keresési szolgáltatás használhassa a Key Vault-kulcsot, bizonyos hozzáférési engedélyeket kell adnia a keresési szolgáltatásnak.

A hozzáférési engedélyek bármikor visszavonhatók. Visszavonása után a keresési szolgáltatás indexe vagy szinonima leképezése, amely a key vault használ, használhatatlanná válik. A key vault hozzáférési engedélyek későbbi visszaállítása visszaállítja az index\synonym map access.Restore Key vault access at a later time will restore index\synonym map access. További információ: [Biztonságos hozzáférés a kulcstartóhoz.](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)

1. [Jelentkezzen be az Azure Portalra,](https://portal.azure.com) és nyissa meg a key vault áttekintő lapját. 

1. Válassza ki az **Access házirendek** beállítását a bal oldali navigációs ablakban, majd kattintson az **+Új hozzáadása**gombra.

   ![Új kulcstartó-hozzáférési házirend hozzáadása](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Új kulcstartó-hozzáférési házirend hozzáadása")

1. Kattintson **az Egyszerű kiválasztása** gombra, és válassza ki az Azure Cognitive Search szolgáltatást. A felügyelt identitás engedélyezése után megjelenítendő objektumazonosító alapján keresheti meg.

   ![Válassza ki a kulcstartó hozzáférési házirendjének egyszerű elemét](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Válassza ki a kulcstartó hozzáférési házirendjének egyszerű elemét")

1. Kattintson a **Kulcsengedélyek elemre,** és válassza *a Get*, *Kicsomagolja a kulcsot* és a *Körbefolyatási kulcsot.* Az Azure *Data Lake Storage vagy* az Azure Storage sablon segítségével gyorsan kiválaszthatja a szükséges engedélyeket.

   Az Azure Cognitive Search szolgáltatást a következő [hozzáférési engedélyekkel](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)kell megadni:

   * *Get* - lehetővé teszi, hogy a keresési szolgáltatás lekérje a kulcs nyilvános részeit egy Key Vaultban
   * *Wrap Key* - lehetővé teszi, hogy a keresési szolgáltatás használja a kulcsot, hogy megvédje a belső titkosítási kulcs
   * *Kulcs kicsomagolása* - lehetővé teszi, hogy a keresési szolgáltatás a kulcs segítségével kicsomagolja a belső titkosítási kulcsot

   ![Kulcstartó hozzáférési házirend-kulcsának engedélyeinek kiválasztása](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Kulcstartó hozzáférési házirend-kulcsának engedélyeinek kiválasztása")

1. Kattintson az OK **gombra,** és mentse a hozzáférési házirend módosításait. **OK**

> [!Important]
> Az Azure Cognitive Search titkosított tartalma úgy van beállítva, hogy egy adott Azure Key Vault-kulcsot használjon egy adott **verzióval.** Ha módosítja a kulcsot vagy a verziót, az indexet vagy a szinonimát frissíteni kell az új kulcs\verzió használatához az előző kulcs\verzió törlése **előtt.** Ennek elmulasztása használhatatlanná teszi az indexet vagy a szinonimát, és nem tudja visszafejteni a tartalmat, ha a kulcshozzáférés elveszik.   

## <a name="5---encrypt-content"></a>5 - Tartalom titkosítása

Az Azure Portal használatával még nem lehet indexet vagy szinonimát leképezni az ügyfél által felügyelt kulccsal titkosítottan. Az Azure Cognitive Search REST API-val ilyen indexet vagy szinonimát hozhat létre.

Mind az index, mind a szinonima leképezése támogatja a kulcs megadásához használt új legfelső szintű **encryptionKey** tulajdonságot. 

Az **Uri kulcstartó**, **a kulcsnév** és a key vault **kulcskulcs kulcsverziójával** létrehozhatunk egy **encryptionKey-definíciót:**

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Ezek közül a key vault-adatok egyike sem tekinthető titkosnak, és könnyen lekérdezhető az Azure Portal on-t az Azure-portál megfelelő Azure Key Vault-kulcslapjára való böngészéssel.

Ha felügyelt identitás helyett AAD-alkalmazást használ a Key Vault-hitelesítéshez, adja hozzá az AAD-alkalmazás **hozzáférési hitelesítő adatait** a titkosítási kulcshoz: 
```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

## <a name="example-index-encryption"></a>Példa: Indextitkosítás
Az új index rest API-n keresztültörténő létrehozásának részletei megtalálhatók a [Create Index (Azure Cognitive Search REST API) címen,](https://docs.microsoft.com/rest/api/searchservice/create-index)ahol az egyetlen különbség itt a titkosítási kulcs részleteinek megadása az indexdefiníció részeként: 

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
 "encryptionKey": {
   "keyVaultUri": "https://demokeyvault.vault.azure.net",
   "keyVaultKeyName": "myEncryptionKey",
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
Most már elküldheti az index létrehozási kérelmét, majd a szokásos módon használhatja az indexet.

## <a name="example-synonym-map-encryption"></a>Példa: Szinonimatérkép-titkosítás

Az új szinonimatérkép REST API-n keresztüli létrehozásának részletei a [Create Synonym Map (Azure Cognitive Search REST API) címen](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)találhatók, ahol az egyetlen különbség itt a titkosítási kulcs részleteinek megadása a szinonimatérkép-definíció részeként: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
Most már elküldheti a szinonima térkép létrehozási kérelmet, majd indítsa el a szokásos módon.

>[!Important] 
> Bár **a encryptionKey** nem adható hozzá a meglévő Azure Cognitive Search-indexek vagy szinonima leképezések, frissíthető azáltal, hogy a három kulcstartó adatait (például a kulcsverzió frissítése) különböző értékeket. Amikor új Key Vault-kulcsra vagy új kulcsverzióra vált, az Azure Cognitive Search indexét vagy szinonimát leképezőjét, amely a kulcsot használja, először frissíteni kell az új kulcs\verzió használatához az előző kulcs\verzió törlése **előtt.** Ennek elmulasztása az indexet vagy a szinonimát használhatatlanná teszi, mivel a kulcshozzáférés elvészután nem tudja visszafejteni a tartalmat.   
> A kulcstartó hozzáférési engedélyeinek későbbi visszaállítása visszaállítja a tartalomhozzáférést.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Speciális: Külsőleg felügyelt Azure Active Directory-alkalmazás használata

Ha egy felügyelt identitás nem lehetséges, létrehozhat egy Azure Active Directory-alkalmazást az Azure Cognitive Search szolgáltatás rendszerbiztonsági taggal. A felügyelt identitás a következő feltételek mellett nem életképes:

* Közvetlenül nem adhat hozzáférést a keresési szolgáltatás hozzáférési engedélyeihez a key vaulthoz (például ha a keresési szolgáltatás az Azure Key Vaulttól eltérő Active Directory-bérlőben található).

* Egyetlen keresési szolgáltatás szükséges több titkosított indexek\szinonima leképezések, mindegyik egy másik kulcs egy másik key vault, ahol minden key vault kell használni **a különböző identitású** hitelesítéshez. Ha egy másik identitás különböző kulcstartók kezelésére nem követelmény, fontolja meg a fenti felügyelt identitás beállítás használata.  

Az ilyen topológiák befogadására az Azure Cognitive Search támogatja az Azure Active Directory (AAD) alkalmazások használatát a keresési szolgáltatás és a Key Vault közötti hitelesítéshez.    
AAD-alkalmazás létrehozása a portálon:

1. [Egy Azure Active Directory-alkalmazás létrehozása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Az alkalmazásazonosító és a hitelesítési kulcs beszerezni,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) mivel azok a titkosított index létrehozásához szükségesek. A megadandó értékek közé tartozik **az alkalmazásazonosító** és **a hitelesítési kulcs.**

>[!Important]
> Amikor úgy dönt, hogy egy AAD-alkalmazás hitelesítési helyett felügyelt identitás, fontolja meg azt a tényt, hogy az Azure Cognitive Search nem jogosult az AAD-alkalmazás kezelésére az Ön nevében, és ez rajtad múlik, hogy kezelje az AAD-alkalmazás, például időszakos az alkalmazás hitelesítési kulcsának elforgatása.
> A AAD-alkalmazás vagy annak hitelesítési kulcsának módosításakor az adott alkalmazást használó Azure Cognitive Search-indexet vagy szinonima-leképezést először frissíteni kell az új alkalmazásazonosító\kulcs használatához az előző alkalmazás vagy annak engedélyezési kulcsának törlése **előtt,** és mielőtt visszavonná a Key Vault-hozzáférést.
> Ennek elmulasztása az indexet vagy a szinonimát használhatatlanná teszi, mivel a kulcshozzáférés elvészután nem tudja visszafejteni a tartalmat.   

## <a name="next-steps"></a>További lépések

Ha nem ismeri az Azure biztonsági architektúráját, tekintse át az [Azure Security dokumentációját,](https://docs.microsoft.com/azure/security/)és különösen ezt a cikket:

> [!div class="nextstepaction"]
> [Adatok titkosítása inaktív](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
