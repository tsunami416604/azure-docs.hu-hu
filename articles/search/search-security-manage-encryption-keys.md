---
title: REST-alapú titkosítás az ügyfél által felügyelt kulcsokkal
titleSuffix: Azure Cognitive Search
description: Az indexeken és az Azure-ban található szinonimák használatával kiegészítheti a kiszolgálóoldali titkosítást, Cognitive Search a Azure Key Vaultban létrehozott és felügyelt kulcsokkal.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: ed5d1f5b35bc9b6dee234678fa82af95e1d53bc7
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553996"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Ügyfél által felügyelt kulcsok konfigurálása az adattitkosításhoz az Azure-ban Cognitive Search

Az Azure Cognitive Search automatikusan titkosítja az indexelt tartalmat a [szolgáltatás által felügyelt kulcsokkal](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). Ha további védelemre van szükség, az alapértelmezett titkosítás kiegészítő titkosítási réteggel kiegészíthető a Azure Key Vaultban létrehozott és kezelt kulcsok használatával. Ez a cikk végigvezeti a CMK-titkosítás beállításának lépésein.

A CMK-titkosítás [Azure Key Vaulttól](https://docs.microsoft.com/azure/key-vault/key-vault-overview)függ. Létrehozhatja saját titkosítási kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-jait a titkosítási kulcsok létrehozásához. A Azure Key Vault használatával a [naplózás engedélyezésekor](../key-vault/general/logging.md)is naplózhatja a kulcshasználat.  

Az ügyfél által felügyelt kulcsokkal történő titkosítást az egyes indexekre vagy a szinonimák leképezésére alkalmazza a rendszer, amikor ezek az objektumok létre lettek hozva, és nincs megadva a keresési szolgáltatás szintjén. Csak az új objektumokat lehet titkosítani. A már létező tartalmak nem titkosíthatók.

A kulcsoknak nem kell ugyanabban a kulcstartóban lenniük. Egyetlen keresési szolgáltatás több titkosított indexet vagy szinonimát is képes tárolni, amelyek mindegyike a saját ügyfél által felügyelt titkosítási kulcsokkal van titkosítva, különböző kulcstartókban tárolva. Az indexek és a szinonimák is használhatók ugyanabban a szolgáltatásban, amely nem titkosított az ügyfél által felügyelt kulcsokkal. 

## <a name="double-encryption"></a>Dupla titkosítás

A 2020. augusztus 1. és bizonyos régiókban létrehozott szolgáltatások esetében a CMK-titkosítás hatóköre ideiglenes lemezeket tartalmaz, így a [teljes kettős titkosítás](search-security-overview.md#double-encryption)érhető el, amely jelenleg elérhető ezekben a régiókban: 

+ USA 2. nyugati régiója
+ USA keleti régiója
+ USA déli középső régiója
+ USA-beli államigazgatás – Virginia
+ USA-beli államigazgatás – Arizona

Ha más régiót használ, vagy az augusztus 1. előtt létrehozott szolgáltatást használja, akkor a CMK-titkosítás csak az adatlemezre korlátozódik, a szolgáltatás által használt ideiglenes lemezek kivételével.

## <a name="prerequisites"></a>Előfeltételek

Ebben a példában a következő szolgáltatásokat és szolgáltatásokat használjuk. 

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 

+ [Hozzon létre egy Azure Key Vault erőforrást](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) , vagy keressen egy meglévő tárat ugyanabban az előfizetésben, mint az Azure Cognitive Search. Ehhez a szolgáltatáshoz azonos előfizetésre van szükség.

+ A konfigurációs feladatokhoz [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) vagy [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) van használatban.

+ A [Poster](search-get-started-postman.md), a [Azure PowerShell](search-create-index-rest-api.md) és a [.net SDK előzetes](https://aka.ms/search-sdk-preview) verziója a titkosítási kulcs paraméterét tartalmazó indexeket és szinonimákat létrehozó REST API meghívására használható. Jelenleg nem támogatott a kulcsok indexekhez vagy szinonimához való hozzáadásához szükséges portál támogatása.

>[!Note]
> Az ügyfél által felügyelt kulcsokkal való titkosítás jellegéből adódóan az Azure Cognitive Search nem fogja tudni lekérni az adatait, ha az Azure Key Vault-kulcsot törlik. Ha meg szeretné akadályozni az adatvesztést a véletlen Key Vaulti törlések miatt, a Key vaulton engedélyezni kell a törlés és a kiürítés védelmét. A Soft-delete alapértelmezés szerint engedélyezve van, ezért csak akkor lesz probléma, ha szándékosan letiltotta. A védelem kiürítése alapértelmezés szerint nincs engedélyezve, de az Azure Cognitive Search CMK titkosításhoz szükséges. [További információ: a](../key-vault/key-vault-ovw-soft-delete.md) védelmi áttekintések eltávolítása és [kiürítése](../key-vault/general/soft-delete-overview.md#purge-protection) .

## <a name="1---enable-key-recovery"></a>1 – kulcshelyreállítás engedélyezése

A Key vaultnak engedélyezve kell lennie a helyreállítható **törlési** és **kiürítési védelemmel** . Ezeket a szolgáltatásokat a portálon vagy a következő PowerShell-vagy Azure CLI-parancsokkal állíthatja be.

### <a name="using-powershell"></a>A PowerShell használata

1. Az `Connect-AzAccount` Azure-beli hitelesítő adatok beállításához futtassa a parancsot.

1. Futtassa a következő parancsot a Key vaulthoz való kapcsolódáshoz, `<vault_name>` érvényes névvel helyettesítve:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. A Azure Key Vault a törlés engedélyezve beállítással jön létre. Ha le van tiltva a tárolón, futtassa a következő parancsot:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Kiürítési védelem engedélyezése:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Mentse a frissítéseket:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 – új kulcs létrehozása

Ha meglévő kulcsot használ az Azure Cognitive Search-tartalom titkosításához, hagyja ki ezt a lépést.

1. [Jelentkezzen be Azure Portalba](https://portal.azure.com) , és nyissa meg a Key Vault áttekintés lapját.

1. Válassza a bal oldali navigációs ablaktáblán a **kulcsok** beállítást, majd kattintson a **+ Létrehozás/importálás**elemre.

1. A **kulcs létrehozása** ablaktáblán a **Beállítások**listájából válassza ki a kulcs létrehozásához használni kívánt módszert. **Létrehozhat egy új** kulcsot, **feltöltheti** a meglévő kulcsot, vagy használhatja a **visszaállítás biztonsági mentést** a kulcsok biztonsági másolatának kiválasztásához.

1. Adja meg a kulcs **nevét** , és ha kívánja, válassza ki a többi fontos tulajdonságot.

1. A telepítés elindításához kattintson a **Létrehozás** gombra.

Jegyezze fel a kulcs azonosítóját – ez a **kulcs értékének URI-ja**, a **kulcs neve**és a **kulcs verziószáma**. Ezeket a titkosított indexek megadásához kell megadni az Azure Cognitive Searchban.
 
![Új Key Vault-kulcs létrehozása](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Új Key Vault-kulcs létrehozása")

## <a name="3---create-a-service-identity"></a>3 – szolgáltatás identitásának létrehozása

Ha identitást rendel a keresési szolgáltatáshoz, Key Vault hozzáférési engedélyeket adhat meg a keresési szolgáltatáshoz. A keresési szolgáltatás a személyazonosságát fogja használni az Azure Key vaultban való hitelesítéshez.

Az Azure Cognitive Search az identitás hozzárendelésének két módját támogatja: felügyelt identitást vagy külsőleg felügyelt Azure Active Directory alkalmazást. 

Ha lehetséges, használjon felügyelt identitást. Ez a legegyszerűbb módja annak, hogy identitást rendeljen a keresési szolgáltatáshoz, és a legtöbb esetben működjön. Ha az indexekhez és a szinonimához több kulcsot használ, vagy ha a megoldás olyan elosztott architektúrában van, amely nem jogosult az identitás-alapú hitelesítésre, használja a cikk végén ismertetett, [külsőleg felügyelt Azure Active Directory megközelítést](#aad-app) .

 Általánosságban elmondható, hogy egy felügyelt identitás lehetővé teszi a keresési szolgáltatás hitelesítését Azure Key Vault a hitelesítő adatok kódban való tárolása nélkül. Az ilyen típusú felügyelt identitás életciklusa a keresési szolgáltatás életciklusához van kötve, amely csak egyetlen felügyelt identitással rendelkezhet. [További információ a felügyelt identitásokról](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. [Jelentkezzen be Azure Portalba](https://portal.azure.com) , és nyissa meg a keresési szolgáltatás áttekintés lapját. 

1. A bal oldali navigációs ablaktáblán kattintson az **Identity (identitás** ) elemre, módosítsa az állapotát **a be**értékre, majd kattintson a **Mentés**gombra.

![Felügyelt identitás engedélyezése](./media/search-enable-msi/enable-identity-portal.png "A Mangal Identity engedélyezése")

## <a name="4---grant-key-access-permissions"></a>4 – kulcsokra vonatkozó hozzáférési engedélyek megadása

Ha engedélyezni szeretné, hogy a keresési szolgáltatás használhassa a Key Vault kulcsot, bizonyos hozzáférési engedélyeket kell adnia a keresési szolgáltatásnak.

A hozzáférési engedélyeket bármikor visszavonhatja. A visszavonás után a Key vaultot használó keresési szolgáltatás indexe vagy szinonimája használhatatlan lesz. A Key Vault-hozzáférési engedélyek későbbi visszaállításakor a rendszer visszaállítja a index\synonym-leképezési hozzáférést. További információt a [kulcstartó biztonságos elérését](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)ismertető témakörben talál.

1. [Jelentkezzen be Azure Portalba](https://portal.azure.com) , és nyissa meg a Key Vault áttekintés lapját. 

1. Válassza ki a **hozzáférési házirendek** beállítást a bal oldali navigációs ablaktáblán, és kattintson az **+ új hozzáadása**lehetőségre.

   ![Új Key Vault hozzáférési szabályzat hozzáadása](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Új Key Vault hozzáférési szabályzat hozzáadása")

1. Kattintson a **rendszerbiztonsági tag kiválasztása** elemre, és válassza ki az Azure Cognitive Search szolgáltatást. A név vagy a felügyelt identitás engedélyezése után megjelenő objektumazonosító alapján is megkeresheti.

   ![A Key Vault hozzáférési szabályzatának kiválasztása](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "A Key Vault hozzáférési szabályzatának kiválasztása")

1. Kattintson a **legfontosabb engedélyek** elemre, és válassza a *beolvasás*, a *kulcs* és a *betakarás kulcsa*lehetőséget. A szükséges engedélyek gyors kiválasztásához használhatja a *Azure Data Lake Storage vagy az Azure Storage* -sablont.

   Az Azure Cognitive Search a következő [hozzáférési engedélyekkel](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)kell megadni:

   * *Get* -lehetővé teszi, hogy a keresési szolgáltatás lekérje a kulcs nyilvános részeit egy Key Vault
   * *Wrap Key* – lehetővé teszi, hogy a keresési szolgáltatás a kulcs használatával megvédje a belső titkosítási kulcsot
   * *Kicsomagolási kulcs* – lehetővé teszi, hogy a keresési szolgáltatás a kulcsot használja a belső titkosítási kulcs kicsomagolásához.

   ![Válassza ki a Key Vault hozzáférési szabályzatának kulcsának engedélyeit](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Válassza ki a Key Vault hozzáférési szabályzatának kulcsának engedélyeit")

1. A **titkos engedélyek**esetében válassza a *beolvasás*lehetőséget.

1. A **tanúsítvány engedélyeihez**válassza a *beolvasás*lehetőséget.

1. Kattintson **az OK** gombra, és **mentse** a hozzáférési szabályzat módosításait.

> [!Important]
> Az Azure Cognitive Search titkosított tartalma úgy van konfigurálva, hogy egy meghatározott Azure Key Vault-kulcsot használjon egy adott **verzióval**. Ha megváltoztatja a kulcsot vagy a verziót, az indexet vagy a szinonima-térképet frissíteni kell az új key\version használatára az előző key\version. törlése **előtt** . Ha ezt nem teszi meg, az index vagy a szinonimák leképezése használhatatlan lesz, a kulcs elérésének elvesztése után nem fogja tudni visszafejteni a tartalmat.   

## <a name="5---encrypt-content"></a>5 – tartalom titkosítása

Ha egy ügyfél által felügyelt kulcsot szeretne felvenni egy indexre vagy szinonimára, akkor a [Search REST API](https://docs.microsoft.com/rest/api/searchservice/) vagy egy SDK-t kell használnia. A portál nem teszi lehetővé a szinonimák leképezését vagy a titkosítási tulajdonságokat. Ha érvényes API-t használ, az indexek és a szinonima-leképezések is támogatják a legfelső szintű **encryptionKey** tulajdonságot. 

A Key Vault **URI-ja**, a **kulcs neve** és a Key Vault kulcsának **verziószáma** alapján hozzon létre egy **encryptionKey** -definíciót az alábbiak szerint:

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
> Ezek a kulcstartó-részletek egyike sem minősül titkosnak, és könnyen lekérhető, ha megkeresi a megfelelő Azure Key Vault kulcsot tartalmazó lapot Azure Portal.

Ha HRE alkalmazást használ a Key Vault hitelesítéshez felügyelt identitás használata helyett, adja hozzá a HRE-alkalmazás **hozzáférési hitelesítő adatait** a titkosítási kulcshoz: 
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

## <a name="example-index-encryption"></a>Példa: index encryption
Az új indexnek a REST API használatával történő létrehozásának részletei a [create index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)helyen találhatók, ahol az egyetlen különbség a titkosítási kulcs részleteinek megadása az index definíciójának részeként: 

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
Most már elküldheti az index-létrehozási kérelmet, majd megkezdheti a normál index használatát.

## <a name="example-synonym-map-encryption"></a>Példa: szinonimák leképezésének titkosítása

Az új szinonimák leképezésének a REST API használatával történő létrehozásának részleteit a [szinonimák leképezése (Azure Cognitive Search REST API) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)című rész tartalmazza, ahol az egyetlen különbség a titkosítási kulcs részleteinek megadása a szinonimák leképezése definíciójának részeként: 

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
Most már elküldheti a szinonima-hozzárendelési kérést, majd normál módon megkezdheti a használatát.

>[!Important] 
> Habár a **encryptionKey** nem vehetők fel a meglévő Azure Cognitive Search indexekhez vagy szinonimái térképekhez, a három kulcstartó részleteinek (például a kulcs verziójának frissítése) különböző értékeinek megadásával lehet frissíteni. Új Key Vault kulcsra vagy új kulcs verzióra való váltáskor a kulcsot használó összes Azure Cognitive Search indexet vagy szinonima-térképet először frissíteni kell az új key\version használatára az előző key\version. törlése **előtt** . Ha ezt nem teszi meg, az index vagy a szinonimák leképezése használhatatlan lesz, mivel a kulcs elérésének elvesztése után nem tudja visszafejteni a tartalmat.   
> A Key Vault-hozzáférési engedélyek későbbi visszaállításával visszaállíthatja a tartalom-hozzáférést.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Speciális: külsőleg felügyelt Azure Active Directory alkalmazás használata

Ha egy felügyelt identitás nem lehetséges, létrehozhat egy Azure Active Directory alkalmazást az Azure Cognitive Search szolgáltatáshoz tartozó rendszerbiztonsági tag használatával. A felügyelt identitások nem életképesek az alábbi feltételek teljesülése esetén:

* A keresési szolgáltatás hozzáférési engedélyeit nem lehet közvetlenül a kulcstartóhoz adni (például ha a keresési szolgáltatás más Active Directory bérlő, mint a Azure Key Vault).

* Egyetlen keresési szolgáltatás szükséges több titkosított indexes\synonym-Térkép üzemeltetéséhez, amelyek mindegyike egy másik Kulcstartóból származó másik kulcsot használ, ahol minden kulcstartónak **más identitást** kell használnia a hitelesítéshez. Ha más identitást használ a különböző kulcstartók kezelésére, érdemes lehet a fenti felügyelt identitást használni.  

Az ilyen topológiák befogadásához az Azure Cognitive Search támogatja Azure Active Directory (HRE) alkalmazások használatát a keresési szolgáltatás és a Key Vault közötti hitelesítéshez.    
HRE-alkalmazás létrehozása a portálon:

1. [Egy Azure Active Directory-alkalmazás létrehozása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Szerezze be az alkalmazás azonosítóját és a hitelesítési kulcsot](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) , mivel ezek a titkosított indexek létrehozásához szükségesek lesznek. A megadható értékeknek tartalmaznia kell az **alkalmazás azonosítóját** és a **hitelesítési kulcsot**.

>[!Important]
> Ha úgy dönt, hogy felügyelt identitás helyett HRE-alkalmazást használ, vegye figyelembe, hogy az Azure Cognitive Search nem rendelkezik jogosultsággal a HRE-alkalmazásnak az Ön nevében történő felügyeletére, és a HRE-alkalmazás kezelése (például az alkalmazás-hitelesítési kulcs rendszeres elforgatása).
> Egy HRE-alkalmazás vagy annak hitelesítési kulcsának módosításakor az adott alkalmazást használó Azure Cognitive Search indexet vagy szinonima-térképet először frissíteni kell az új alkalmazás-ID\key használatára az előző alkalmazás vagy az engedélyezési kulcs törlése **előtt** , és a Key Vault hozzáférésének visszavonása előtt.
> Ha ezt nem teszi meg, az index vagy a szinonimák leképezése használhatatlan lesz, mivel a kulcs elérésének elvesztése után nem tudja visszafejteni a tartalmat.

## <a name="work-with-encrypted-content"></a>Titkosított tartalom használata

A CMK titkosítással a további titkosítási/visszafejtési munkák miatt az indexelés és a lekérdezések késése is megfigyelhető. Az Azure Cognitive Search nem naplózza a titkosítási tevékenységeket, de a Key Vault naplózási szolgáltatáson keresztül figyelheti a kulcsokhoz való hozzáférést. Javasoljuk, hogy [engedélyezze a naplózást](../key-vault/general/logging.md) a Key Vault beállításának részeként.

A kulcs rotációja várhatóan az idő múlásával történik. A kulcsok elforgatásakor fontos, hogy kövesse ezt a sorozatot:

1. [Az index vagy a szinonima Térkép által használt kulcs meghatározása](search-security-get-encryption-keys.md).
1. [Hozzon létre egy új kulcsot a Key vaultban](../key-vault/keys/quick-create-portal.md), de hagyja elérhetővé az eredeti kulcsot.
1. Az új értékek használatához [frissítse a encryptionKey tulajdonságait](https://docs.microsoft.com/rest/api/searchservice/update-index) egy index vagy szinonima leképezésben. Csak azokat az objektumokat lehet frissíteni, amelyeket eredetileg ezzel a tulajdonsággal hoztak létre, hogy más értéket használjanak.
1. Tiltsa le vagy törölje az előző kulcsot a Key vaultban. A kulcs-hozzáférés figyelése az új kulcs használatának ellenőrzéséhez.

A keresési szolgáltatás teljesítménybeli okokból akár több óráig is gyorsítótárazza a kulcsot. Ha letiltja vagy törli a kulcsot anélkül, hogy újat kellene benyújtania, a lekérdezések ideiglenesen is működni fognak, amíg a gyorsítótár le nem jár. Ha azonban a keresési szolgáltatás nem tudja visszafejteni a tartalmat, a következő üzenet jelenik meg: "Hozzáférés megtiltva. Lehetséges, hogy a használt lekérdezési kulcsot visszavonták – próbálkozzon újra. " 

## <a name="next-steps"></a>Következő lépések

Ha nem ismeri az Azure biztonsági architektúráját, tekintse át az [Azure biztonsági dokumentációját](https://docs.microsoft.com/azure/security/), és különösen a következő cikket:

> [!div class="nextstepaction"]
> [Inaktív adatok titkosítása](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
