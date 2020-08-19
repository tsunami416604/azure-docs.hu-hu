---
title: Hitelesítés Azure Key Vault
description: Útmutató a Azure Key Vault való hitelesítéshez
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 7645600a476a1c2294ddd4a24fe01e2ffe51d5ac
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589982"
---
# <a name="authenticate-to-azure-key-vault"></a>Hitelesítés Azure Key Vault

## <a name="overview"></a>Áttekintés

A Azure Key Vault egy olyan titkokat kezelő megoldás, amellyel központilag kezelhető az alkalmazás titkainak tárolása, és szabályozható azok eloszlása. Azure Key Vault szükségtelenné teszi a hitelesítő adatok tárolását az alkalmazásokban. Az alkalmazás képes hitelesíteni a Key vaultot a szükséges hitelesítő adatok lekéréséhez. Ez a dokumentum a Key Vault hitelesítésének alapjait fedi le.

Ez a dokumentum segítséget nyújt a Key Vault-hitelesítés működésének megismerésében. Ez a dokumentum ismerteti a hitelesítési folyamatot, bemutatjuk, hogyan adhat hozzáférést a kulcstartóhoz, és tartalmaz egy oktatóanyagot, amely egy minta Python-alkalmazásból a Key vaultban tárolt titkos kulcsot kér le.

Ez a dokumentum a következőket foglalja magában:

* Fő fogalmak
* Rendszerbiztonsági tag regisztrációja
* Key Vault hitelesítési folyamat ismertetése
* Egyszerű szolgáltatáshoz való hozzáférés biztosítása Key Vault
* Oktatóanyag (Python)

## <a name="key-concepts"></a>Fő fogalmak

### <a name="azure-active-directory-concepts"></a>Azure Active Directory fogalmak

* Azure Active Directory (HRE) – Azure Active Directory (Azure AD) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása, amely segít az alkalmazottaknak bejelentkezni és hozzáférni az erőforrásokhoz

* Szerepkör-definíció – A szerepkör-definíció az engedélyek gyűjteménye.  A HRE rendelkezik olyan szabványos szerepkörökkel (tulajdonos, közreműködő vagy olvasó), amely az Azure-erőforrások olvasási, írási és törlési műveleteinek engedélyezési szintjeit tartalmazza. A szerepkörök olyan egyéni definíciók is lehetnek, amelyeket a felhasználók konkrét részletességi engedélyekkel hoztak létre.

* Alkalmazás regisztrálása – ha Azure AD-alkalmazást regisztrál, két objektum jön létre az Azure AD-bérlőben, egy alkalmazás-objektumban és egy egyszerű szolgáltatásnév objektumban. Tekintse át az Application objektumot az alkalmazás globális ábrázolásának az összes bérlőn való használatra, valamint az egyszerű szolgáltatásnév, amely egy adott bérlőn való használatra helyi ábrázolás.

### <a name="security-principal-concepts"></a>Rendszerbiztonsági tag – fogalmak

* Rendszerbiztonsági tag – A rendszerbiztonsági tag egy olyan objektum, amely az Azure-erőforrásokhoz való hozzáférést kérő felhasználót, csoportot, egyszerű szolgáltatásnevet vagy felügyelt identitást jelöl.

* Felhasználó – Egy Azure Active Directory-profillal rendelkező személy.

* Csoport – Felhasználók az Azure Active Directoryban létrehozott csoportja. Ha egy csoporthoz rendel hozzá egy szerepkört, a csoportban lévő összes felhasználó rendelkezik majd az adott szerepkörrel.

* Szolgáltatásnév – Alkalmazások vagy szolgáltatások által adott Azure-erőforrások elérésére használt biztonsági identitás. Úgy képzelhető el, mint egy alkalmazás felhasználói identitása (felhasználóneve és jelszava vagy tanúsítványa).

* Felügyelt identitás – az Azure által automatikusan kezelt Azure Active Directory identitás.

* Objektumazonosító (ügyfél-azonosító) – az Azure AD által generált egyedi azonosító, amely a kezdeti kiépítés során egy egyszerű szolgáltatáshoz van kötve.

## <a name="security-principal-registration"></a>Rendszerbiztonsági tag regisztrációja

1. A rendszergazda Azure Active Directoryban regisztrál egy felhasználót vagy alkalmazást (egyszerű szolgáltatásnév).

2. A rendszergazda létrehoz egy Azure Key Vault, és konfigurálja a hozzáférési házirendeket (ACL-eket).

3. Választható A rendszergazda konfigurálja a Azure Key Vault tűzfalat.

![KÉP](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>A Key Vault hitelesítési folyamat ismertetése

1. Az egyszerű szolgáltatásnév meghívja a hitelesítést a HRE, ez több módon is megtörténhet:
    * A felhasználók felhasználónevet és jelszót is bejelentkezhetnek a Azure Portalba.
    * Egy alkalmazás ügyfél-azonosítót használ, és a HRE ügyfél-titkos vagy ügyféltanúsítvány-tanúsítványt jelenít meg.
    * Egy Azure-erőforrás, például egy virtuális gép rendelkezik egy hozzárendelt MSI-vel, és kapcsolatba lép a IMDS REST-végponttal egy hozzáférési jogkivonat beszerzéséhez.

2. Ha a HRE hitelesítése sikeres, az egyszerű szolgáltatásnév OAuth-tokent kap.
3. Az egyszerű szolgáltatás meghívja a Key Vault.
4. Azure Key Vault a tűzfal meghatározza, hogy engedélyezi-e a hívást.
    * 1. forgatókönyv: Key Vault tűzfal le van tiltva, a kulcstartó nyilvános végpontja (URI) elérhető a nyilvános internetről. A hívás engedélyezett.
    * 2. forgatókönyv: a hívó egy Azure Key Vault megbízható szolgáltatás. Bizonyos Azure-szolgáltatások megkerülhetik a Key Vault tűzfalát, ha a lehetőség ki van választva. [Key Vault megbízható szolgáltatások listája](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * 3. forgatókönyv: a hívó a Azure Key Vault tűzfalon IP-cím, virtuális hálózat vagy szolgáltatási végpont alapján van felsorolva.
    * 4. forgatókönyv: a hívó elérheti Azure Key Vault egy konfigurált magánhálózati kapcsolaton keresztül.
    * 5. forgatókönyv: a hívó nem engedélyezett, és a rendszer egy tiltott választ ad vissza.
5. Key Vault kezdeményezi a HRE az egyszerű szolgáltatásnév hozzáférési jogkivonatának érvényesítéséhez.
6. Key Vault ellenőrzi, hogy az egyszerű szolgáltatásnév rendelkezik-e a kért művelet végrehajtásához szükséges engedélyekkel, ebben a példában a művelet titkos lesz.
7. A Key Vault az egyszerű szolgáltatásnév számára biztosít titkos kulcsot.

![KÉP](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>Egyszerű szolgáltatáshoz való hozzáférés biztosítása Key Vault

1. Ha még nem rendelkezik ilyennel, hozzon létre egy egyszerű szolgáltatásnevet. [Egyszerű szolgáltatás létrehozása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. Adjon hozzá egy szerepkör-hozzárendelést az egyszerű szolgáltatáshoz a Azure Key Vault IAM beállításokban. Hozzáadhat előre hozzárendelt szerepköröket a tulajdonoshoz, a Közreműködőhöz vagy az olvasóhoz. Az egyszerű szolgáltatásnév egyéni szerepköröket is létrehozhat. Követnie kell a legalacsonyabb jogosultsági szintű rendszerbiztonsági tag követelményeit, és csak a minimálisan szükséges hozzáférést kell biztosítania a szolgáltatáshoz. 
3.  Konfigurálja a Key Vault-tűzfalat. Megtarthatja a Key Vault-tűzfalat, és engedélyezheti a hozzáférést a nyilvános internetről (kevésbé biztonságos, könnyebben konfigurálható). Az adott IP-címtartományok, szolgáltatási végpontok, virtuális hálózatok vagy magánhálózati végpontok (biztonságosabb) elérését is korlátozhatja.
4.  Adjon hozzá egy hozzáférési szabályzatot a szolgáltatásnév számára. Ez az a művelet, amelyet az egyszerű szolgáltatásnév végrehajthat a kulcstartón. A legkevesebb jogosultságot kell használnia, és korlátoznia kell az egyszerű szolgáltatás által végrehajtható műveleteket. Ha azonban nem rendelkezik megfelelő engedélyekkel, az egyszerű szolgáltatásnév hozzáférése megtagadva.

## <a name="tutorial"></a>Oktatóanyag

Ebből az oktatóanyagból megtudhatja, hogyan állíthat be egy egyszerű szolgáltatást a Key Vault hitelesítéséhez és a titkos kulcs lekéréséhez. 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>1. rész: egyszerű szolgáltatásnév létrehozása a Azure Portalban

1. Bejelentkezés az Azure Portalra
1. Azure Active Directory keresése
1. Kattintson az "alkalmazás-regisztrációk" fülre.
1. Kattintson az "+ új regisztráció" elemre.
1. Az egyszerű szolgáltatásnév nevének létrehozása
1. Regisztráció kiválasztása

Ezen a ponton rendelkezik egy regisztrált egyszerű szolgáltatással. Megtekintheti az "alkalmazások regisztrációja" lehetőség kiválasztásával. Az egyszerű szolgáltatásnév most már hozzá lesz rendelve egy ügyfél-azonosító GUID azonosítóhoz, amelynek a neve "username" az egyszerű szolgáltatásnév számára. Most létre kell hoznia egy "jelszót" a szolgáltatásnév számára, használhat egy ügyfél titkot vagy egy ügyféltanúsítványt. Megjegyzés: Ha a hitelesítéshez ügyfél-titkos kulcsot használ, nem biztonságos, és csak tesztelési célokra használható. Ez az oktatóanyag bemutatja, hogyan használhatja az ügyféltanúsítványt.

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>2. rész: ügyféltanúsítvány létrehozása a szolgáltatásnév számára

1. Tanúsítvány létrehozása

    * 1. lehetőség: tanúsítvány létrehozása az [OpenSSL](https://www.openssl.org/) használatával (csak tesztelési célokra, nem használ önaláírt tanúsítványokat éles környezetben)

    ```console
    openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
    ```

    * 2. lehetőség: tanúsítvány létrehozása a Key Vault használatával. [Tanúsítvány létrehozása Azure Key Vaultban](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. A tanúsítvány letöltése PEM formátumban
1. Jelentkezzen be a Azure Portalba, és navigáljon a Azure Active Directory
1. Kattintson az "alkalmazások regisztrálása" elemre.
1. Válassza ki az 1. részben létrehozott szolgáltatásnevet.
1. Kattintson az egyszerű szolgáltatásnév "tanúsítványok és titkok" lapjára
1. Töltse fel a tanúsítványt a "tanúsítvány feltöltése" gomb használatával

### <a name="part-3-configure-an-azure-key-vault"></a>3. rész: Azure Key Vault konfigurálása

1. Azure Key Vault [hivatkozás](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) létrehozása

2. Key Vault IAM-engedélyek konfigurálása
    1. Navigáljon a kulcstartóhoz
    1. Válassza a "Access Control (IAM)" fület
    1. Kattintson a szerepkör-hozzárendelés hozzáadása elemre.
    1. Válassza ki a "közreműködő" szerepkört a legördülő listából
    1. Adja meg a létrehozott egyszerű szolgáltatásnév nevét vagy ügyfél-AZONOSÍTÓját.
    1. Kattintson a szerepkör-hozzárendelések megtekintése elemre, és erősítse meg a szolgáltatásnév listáját

3. Key Vault hozzáférési házirend engedélyeinek konfigurálása
    1. Navigáljon a kulcstartóhoz
    1. Válassza a "hozzáférési szabályzatok" fület a "beállítások" alatt.
    1. Válassza a "+ hozzáférési szabályzat hozzáadása" hivatkozást
    1. A titkos engedélyek legördülő menüben tekintse meg a "Get" és a "List" engedélyeket.
    1. Válassza ki a szolgáltatásnevet név vagy ügyfél-azonosító alapján.
    1. Válassza a "Hozzáadás" lehetőséget.
    1. Válassza a mentés lehetőséget

4. Hozzon létre egy titkos kulcsot a kulcstartóban.
    1. Navigáljon a kulcstartóhoz
    1. Kattintson a "titkok" fülre a beállítások területen.
    1. Kattintson a "+ elő/Importálás" lehetőségre.
    1. Hozzon létre egy nevet a titok számára, ebben a példában a "teszt" nevű titkot nevezem el.
    1. Hozzon létre egy értéket a titok számára, ebben a példában a "password123" értéket kell megadni

Most, amikor kódot futtat a helyi gépről, a hitelesítheti a Key vaultot egy hozzáférési jogkivonat lekérésével, amely az ügyfél-azonosítót és a tanúsítvány elérési útját mutatja be.

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>4. rész: a titkos kulcs beolvasása a Azure Key Vault egy alkalmazásban (Python)

Az alábbi mintakód segítségével tesztelheti, hogy az alkalmazás képes-e titkos kulcsot beolvasni a kulcstartóból a konfigurált egyszerű szolgáltatásnév használatával. 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![KÉP](../media/authentication-3.png)


## <a name="next-steps"></a>Következő lépések

1. Ismerje meg, hogyan lehet elhárítani a Key Vault hitelesítési hibáit. [Key Vault hibaelhárítási útmutató](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
