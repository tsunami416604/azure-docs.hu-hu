---
title: Titkosítás inaktív ügyfél által kezelt kulcsok használata az Azure Key Vault (előzetes verzió) – Azure Search
description: Kiegészítés a kiszolgálóoldali titkosítás az indexeket és a kulcsok létrehozása és kezelése az Azure Key Vault révén az Azure Search szinonimatérképet keresztül.
author: NatiNimni
manager: jlembicz
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: 9d2cd2a2f4b3143d58d0ef03d67de094ea03303e
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523095"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Az Azure Search encryption ügyfél által kezelt kulcsok használata az Azure Key Vaultban

> [!Note]
> Az ügyfél által felügyelt kulcsok titkosítási előzetes állapotban van, nem éles használatra szánt. A [REST API verzióját 2019-05-06-Preview](search-api-preview.md) ezt a szolgáltatást biztosít. A .NET SDK 8.0. dátumú előzetes sémaverzióra is használhatja.
>
> Ez a szolgáltatás ingyenes szolgáltatásokhoz nem érhető el. Ekkor vagy később a 2019-01-01 létrehozott számlázható keresési szolgáltatás kell használnia. Rendszer jelenleg nem portál támogatja.

Alapértelmezés szerint az Azure Search titkosítja az inaktív felhasználói tartalom [szolgáltatás által kezelt kulcsokkal](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest#data-encryption-models). Egy további titkosítási réteget kulcsok létrehozása és kezelése az Azure Key Vault használata az alapértelmezett titkosítási egészítheti ki. Ez a cikk végigvezeti a lépéseken.

Kiszolgálóoldali titkosítás támogatott integrációjával [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Létrehozhat saját titkosítási kulcsok és a key vaultban tárolja őket, vagy használhatja az Azure Key Vault API-k készítése a titkosítási kulcsokat. Az Azure Key Vault is naplózhatja a kulcshasználatot. 

Az ügyfél által felügyelt kulcsok titkosítási index vagy szinten szinonimát térkép létrehozásakor azokat az objektumokat, nem pedig a keresési szolgáltatási szint van beállítva. Tartalom, amely már nem titkosítható. 

A Key vault-kulcstartók különböző különböző kulcsokat is használhat. Ez azt jelenti, hogy egyetlen keresési szolgáltatás több titkosított indexes\synonym térképek, minden egyes titkosítva, esetlegesen különböző ügyfél által felügyelt kulcsot használ, indexes\synonym leképezések nem titkosított ügyfél által kezelt kulcsok használata mellett is üzemeltethet. 

## <a name="prerequisites"></a>Előfeltételek

Ebben a példában a következő szolgáltatásokat használja. 

+ [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben az oktatóanyagban egy ingyenes szolgáltatás használhatja.

+ [Hozzon létre egy Azure Key Vault-erőforrást](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) vagy nem található az előfizetéshez tartozó meglévő-tároló.

+ [Az Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) vagy [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli) -konfigurációs feladatokhoz használatos.

+ [Postman](search-fiddler.md), [Azure PowerShell-lel](search-create-index-rest-api.md) és [Azure Search SDK](https://aka.ms/search-sdk-preview) hívja az előzetes verziójú REST API segítségével. Nincs portál vagy ügyfél által felügyelt titkosítás jelenleg .NET SDK-val támogatása.

## <a name="1---enable-key-recovery"></a>1 - kulcs-helyreállítási engedélyezése

Ez a lépés nem kötelező, de erősen ajánlott. Engedélyezése után az Azure Key Vault-erőforrás létrehozásához, **a helyreállítható Törlés** és **kiürítése védelmi** a kijelölt Key vaultban a következő PowerShell vagy az Azure CLI-parancsok végrehajtásával:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> A titkosítás, az ügyfél által felügyelt kulcsokat a szolgáltatás rendkívül jellege miatt az Azure Search nem sikerült beolvasni az adatokat az Azure Key vault-kulcs törlése. Véletlen Key Vault-kulcsok törlése okozta adatvesztés megelőzése érdekében azt javasoljuk a helyreállítható törlés és a végleges törlés védelem engedélyezése a kiválasztott kulcstartó. További információkért lásd: [Azure Key Vault helyreállítható Törlés](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2 – hozzon létre egy új kulcsot

Ha az Azure Search-tartalom titkosítása a meglévő kulcsot használ, akkor kihagyhatja ezt a lépést.

1. [Jelentkezzen be az Azure portal](https://portal.azure.com) , és keresse meg a kulcstartó-irányítópultra.

1. Válassza ki a **kulcsok** beállítást a bal oldali navigációs panelen, és kattintson a **+ létrehozás/importálás**.

1. Az a **hozzon létre egy kulcsot** panel listájában **beállítások**, válassza ki a kulcs létrehozásához használni kívánt módszert. Is **Generate** egy új kulcsot, **feltöltése** egy meglévő kulcs, vagy használjon **biztonsági másolat visszaállítása** kulcs biztonsági másolatának kiválasztásához.

1. Adjon meg egy **neve** kulcs, és szükség esetén más kulcstulajdonságok kiválasztása.

1. Kattintson a **létrehozás** gombra kattintva indítsa el a központi telepítést.

Jegyezze fel a kulcs azonosítóját – ez tevődik össze a **Uri értéket**, a **kulcsnév**, és a **kulcsverzió**. Szüksége lesz ezekre a titkosított index definiálása az Azure Search szolgáltatásban.
 
![Hozzon létre egy új a key vault-kulcs](./media/search-manage-encryption-keys/create-new-key-vault-key.png "új a key vault-kulcs létrehozása")

## <a name="3---create-a-service-identity"></a>3 – a felügyeltszolgáltatás-identitás létrehozása

Identitás hozzárendelése a keresési szolgáltatás lehetővé teszi, hogy a keresési szolgáltatás Key Vault hozzáférési engedélyeket. A keresési szolgáltatás hitelesítéséhez az Azure Key vault fogja használni a identitását.

Az Azure Search két különböző módon támogatja a identitás hozzárendelése: egy felügyelt identitás vagy külsőleg felügyelt Azure Active Directory-alkalmazás. 

Ha lehetséges használjon egy felügyelt identitás. Ez a legegyszerűbb módja az identitás hozzárendelése a keresési szolgáltatás, és a legtöbb esetben működik. Ha használ több kulcs indexek és szinonimatérképet, vagy ha a megoldás egy felosztott architektúrában, amely disqualifies azonosító-alapú hitelesítést, használja a speciális [külsőleg felügyelt Azure Active Directory megközelítés](#aad-app)Ez a cikk végén bemutatott.

 Egy felügyelt identitás általában lehetővé teszi, hogy a keresési szolgáltatás nélkül is végezhetnek az Azure Key Vaultba hitelesítő adatok tárolása a kódban. Az ilyen típusú felügyelt identitások életciklusát a keresési szolgáltatás, amely csak egyetlen felügyelt identitás életciklusának van kötve. [További tudnivalók a felügyelt identitások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Egy felügyelt identitás létrehozása [toAzure portál bejelentkezés](https://portal.azure.com) , és nyissa meg a search szolgáltatás irányítópultját. 

1. Kattintson a **identitás** a bal oldali navigációs ablaktáblán, állítsa annak állapotát a **a**, és kattintson a **mentése**.

![Egy felügyelt identitás engedélyezése](./media/search-enable-msi/enable-identity-portal.png "manged identitás engedélyezése")

## <a name="4---grant-key-access-permissions"></a>4 – hozzáférés a kulcshoz engedélyek megadása

Ahhoz, hogy a keresési szolgáltatás használata a Key Vault-kulcs, kell megadni a keresési szolgáltatás bizonyos hozzáférési engedélyeket.

Hozzáférési engedélyek egy adott időpontban tudta vissza kell vonni. Miután visszavonja, minden keresési index vagy szinonimát szolgáltatástérkép, hogy a key vault használó használhatatlan lesz. Key vault-hozzáférési engedélyek visszaállítását egy későbbi időpontban index\synonym térkép hozzáférés állítja vissza. További információkért lásd: [biztonságos hozzáférés a key vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Jelentkezzen be az Azure portal](https://portal.azure.com) és a key vault – Áttekintés lap megnyitásához. 

1. Válassza ki a **hozzáférési házirendek** beállítást a bal oldali navigációs panelen, és kattintson a **+ Hozzáadás, új**.

   ![Új kulcstartó-hozzáférési szabályzat hozzáadása](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "új kulcstartó-hozzáférési szabályzat hozzáadása")

1. Kattintson a **válassza egyszerű** , és válassza ki az Azure Search szolgáltatást. Kereshet, név, illetve a felügyelt identitás engedélyezése után megjelenő objektum azonosítója.

   ![Válassza ki a key vault hozzáférési házirend egyszerű](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "jelölje be a key vault hozzáférési házirend rendszerbiztonsági tag")

1. Kattintson a **Kulcsengedélyek** válassza *első*, *kulcs kicsomagolása* és *Wrap Key*. Használhatja a *Azure Data Lake Storage vagy az Azure Storage* sablon gyorsan kiválasztható a szükséges engedélyekkel.

   Az Azure search kell biztosítani az alábbi [hozzáférési engedélyek](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Első* – lehetővé teszi, hogy a keresési szolgáltatás lekérni a kulcsot a Key Vaultban nyilvános részei
   * *Kulcs becsomagolása* – lehetővé teszi a keresési szolgáltatás használatához a kulcs védelme érdekében a belső titkosítási kulcs
   * *Kulcs kicsomagolása* – lehetővé teszi a keresési szolgáltatás használatához a kulcs segítségével a belső titkosítási kulcs kicsomagolása

   ![Válassza ki a kulcstartó hozzáférési házirend Kulcsengedélyek](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "válassza ki a kulcstartó hozzáférési házirend Kulcsengedélyek")

1. Kattintson a **OK** és **mentése** a hozzáférési szabályzatok módosításainak.

> [!Important]
> Az Azure Search szolgáltatásban a titkosított tartalom és a egy adott egy adott Azure Key Vault-kulcs használatára van konfigurálva **verzió**. Ha módosítja a kulcsot vagy a verziója, az index vagy szinonimát térkép frissíteni kell az új key\version használandó **előtt** az előző key\version törlése. Ezt elmulasztja, akkor jelenik meg az index vagy szinonimát használhatatlan leképezése, akkor nem lehet visszafejteni a tartalmat, ha megszakad a hozzáférés a kulcshoz.   

## <a name="5---encrypt-content"></a>5 – tartalom titkosítása

Az ügyfél által felügyelt kulccsal titkosított index vagy szinonimát térkép létrehozása még nem lehetséges az Azure portal használatával. Azure Search REST API használatával az ilyen egy index vagy szinonimát térkép létrehozásához.

Index és a szinonima leképezése támogatási új legfelső szintű **encryptionKey** a kulcs megadásához használt tulajdonságot. 

Használatával a **a key vault Uri**, **kulcsnév** és a **kulcs verziója** a Key vault-kulcs, létrehozhatunk egy **encryptionKey** definíciója:

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
> Ezeket az adatokat a key vault egyike számítanak secret, és navigáljon a megfelelő Azure Key Vault lapja az Azure Portalon az könnyen olvasható.

Ha egy felügyelt identitás használata helyett a Key Vault-hitelesítés az AAD-alkalmazást használ, adja hozzá az AAD-alkalmazás **hozzáférési hitelesítő adatokat** a titkosítási kulcs: 
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

## <a name="example-index-encryption"></a>Példa: Index titkosítás
A REST API-val egy új index létrehozását részletei nem található a(z) [Index létrehozása (Azure Search szolgáltatás REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index), ahol az egyetlen különbség van adja meg a titkosítási kulcs adatai az index definícióját részeként: 

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
Most már küldhet az index létrehozására irányuló kérelem, és indítsa el az index általában használatát.

## <a name="example-synonym-map-encryption"></a>Példa: Szinonima térkép titkosítás

Egy új szinonimatérképet a REST API-n keresztül létrehozásának részleteit található [Szinonimatérkép létrehozása (Azure Search szolgáltatás REST API)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), ahol az egyetlen különbség van adja meg a titkosítási kulcs adatai a szinonima leképezésdefiníció részeként: 

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
Most már küldhet a szinonima térkép létrehozására vonatkozó kérést, és majd a szokásos módon használatának megkezdéséhez.

>[!Important] 
> Miközben **encryptionKey** nem adható hozzá meglévő Azure Search-indexek vagy szinonimát térképek, előfordulhat, hogy frissíthető különböző értékek azáltal, hogy bármely három a key vault részleteit (például a fő verziójának frissítése). Ha módosítja egy új Key Vault-kulcs vagy a kulcs új verziója, a kulcsot használja minden olyan Azure Search index vagy szinonimát térkép először frissíteni kell, hogy használja az új key\version **előtt** az előző key\version törlése. Ezt elmulasztja, akkor jelenik meg az index vagy használhatatlan, nem tudja majd visszafejteni a tartalom egyszer kulcshozzáférés szinonimatérkép elvész.   
> Key vault-hozzáférési engedélyek visszaállítását egy későbbi időpontban tartalom-hozzáférési állítja vissza.

## <a name="aad-app"></a> Speciális: Egy Azure Active Directory külsőleg felügyelt alkalmazás használata

Ha egy felügyelt identitás ez nem lehetséges, létrehozhat egy Azure Active Directory-alkalmazás biztonsági egyszerű az Azure Search szolgáltatás számára. Pontosabban az egy felügyelt identitás nem működőképes, ezen feltételek:

* Nem közvetlenül biztosíthat a keresést a Key vault szolgáltatás a hozzáférési engedélyek (például, ha a keresési szolgáltatás, mint az Azure Key Vault egy másik Active Directory-bérlőben található).

* Egyetlen keresési szolgáltatás szükség több titkosított indexes\synonym térképek, üzemeltetéséhez, minden egyes használatával egy másik kulcsot egy másik Key vaultból, ahol minden egyes a key vault kell használnia **egy másik identitás** a hitelesítéshez. Ha egy másik identitással való kezelése a Key vault-kulcstartók különböző nem követelmény, fontolja meg a fenti felügyelt identitás lehetőséget.  

Az ilyen topológiák befogadásához Azure keresési támogatja az Azure Active Directory (AAD) alkalmazások használata a Key Vault és keresési szolgáltatás közötti hitelesítéshez.    
Az AAD-alkalmazás létrehozása a portálon:

1. [Egy Azure Active Directory-alkalmazás létrehozása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Az Alkalmazásazonosító és hitelesítési kulcs beszerzése](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) , azok titkosított index létrehozásához szükség lesz. Meg kell adnia értékeket tartalmaznak **Alkalmazásazonosító** és **hitelesítési kulcs**.

>[!Important]
> Hitelesítés az AAD-alkalmazás használata helyett egy felügyelt identitás meghatározásakor vegye figyelembe a tény, hogy nem jogosult az Azure Search kezelése az AAD-alkalmazás az Ön nevében, és Ön, mint a rendszeres elforgatási szögét az AAD-alkalmazás kezelése az alkalmazás hitelesítési kulcsát.
> Az AAD-alkalmazás vagy a hitelesítési kulcs módosításakor bármely használó alkalmazást használó Azure Search-index vagy szinonimát térkép először frissíteni kell az új alkalmazás ID\key **előtt** a korábbi alkalmazás törlése vagy a hitelesítési kulcs, és mielőtt visszavonja az a Key Vault elérését.
> Ezt elmulasztja, akkor jelenik meg az index vagy használhatatlan, nem tudja majd visszafejteni a tartalom egyszer kulcshozzáférés szinonimatérkép elvész.   

## <a name="next-steps"></a>További lépések

Ha ismeri az Azure biztonsági architektúra, tekintse át a [Azure Security dokumentációja](https://docs.microsoft.com/azure/security/), és ebben az esetben, ez a cikk:

> [!div class="nextstepaction"]
> [Inaktív adatok titkosítása](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
