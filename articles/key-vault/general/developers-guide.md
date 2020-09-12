---
title: Az Azure Key Vault fejlesztői útmutatója
description: A fejlesztők a Azure Key Vault segítségével kezelhetik a titkosítási kulcsokat a Microsoft Azure környezetben.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: b18ca88f9a97b09b7c5e15e78a2301bee712df5e
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89394754"
---
# <a name="azure-key-vault-developers-guide"></a>Az Azure Key Vault fejlesztői útmutatója

Key Vault lehetővé teszi a bizalmas adatok biztonságos elérését az alkalmazásokon belülről:

- A kulcsok és a titkos kulcsok védelme a kód írása nélkül történik, és az alkalmazások egyszerűen használhatók.
- Saját ügyfelei és saját kulcsaik is kezelhetők, így az alapszintű szoftverek funkcióinak biztosítására koncentrálhat. Így az alkalmazásai nem felelnek meg az ügyfelek bérlői kulcsainak és titkos kulcsainak.
- Az alkalmazás a kulcsokat az aláíráshoz és a titkosításhoz is használhatja, és az alkalmazáson kívül is tartja a kulcskezelő szolgáltatást, így a megoldás földrajzilag elosztott alkalmazásként is használható.
- Key Vault tanúsítványok kezelése. További információ: [tanúsítványok](../certificates/about-certificates.md)

További általános információk a Azure Key Vaultről: [Mi az Key Vault](overview.md)).

## <a name="public-previews"></a>Nyilvános előzetes verziók

Időnként új Key Vault szolgáltatás nyilvános előzetes verzióját bocsátjuk rendelkezésére. Próbálja ki ezeket, és tudassa velünk, hogy mit gondol a azurekeyvault@microsoft.com szolgáltatáson keresztül, a visszajelzési e-mail-címünk alapján.

## <a name="creating-and-managing-key-vaults"></a>Kulcstartók létrehozása és kezelése

Az Azure Key Vault módot kínál a hitelesítő adatok, valamint egyéb kulcsok és titkos kódok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. Az Azure-erőforrások felügyelt identitásai megkönnyítik a probléma megoldását azáltal, hogy az Azure-szolgáltatások automatikusan felügyelt identitást biztosítanak Azure Active Directory (Azure AD). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia. 

Az Azure-erőforrások felügyelt identitásával kapcsolatos további információkért tekintse meg [a felügyelt identitások áttekintése](../../active-directory/managed-identities-azure-resources/overview.md)című témakört. Az Azure AD-vel való használatról további információt az [alkalmazások integrálása a Azure Active Directorysal](../../active-directory/develop/active-directory-integrating-applications.md)című témakörben talál.

A Key vaultban található kulcsok, titkok vagy tanúsítványok használata előtt a Key vaultot a CLI, a PowerShell, a Resource Manager-sablonok vagy a REST használatával hozhatja létre és kezelheti a következő cikkekben leírtak szerint:

- [Kulcstartók létrehozása és kezelése a CLI-vel](quick-create-cli.md)
- [Kulcstartók létrehozása és kezelése a PowerShell-lel](quick-create-powershell.md)
- [Kulcstartók létrehozása és kezelése a Azure Portal](quick-create-portal.md)
- [Kulcstartók létrehozása és kezelése a REST-tel](/rest/api/keyvault/vaults/createorupdate)

### <a name="set-and-retrieve-secrets"></a>Titkok beállítása és beolvasása

- [Titkos kód beállítása és beolvasása a CLI-vel](../secrets/quick-create-cli.md)
- [Titkos kód beállítása és beolvasása a PowerShell-lel](../secrets/quick-create-powershell.md)
- [Titkos kód beállítása és beolvasása a Azure Portal](../secrets/quick-create-portal.md)
- [Secrets műveletek a REST-tel](/rest/api/keyvault/#secret-operations)
- [Titok beállítása és beolvasása Python-val](../secrets/quick-create-python.md)
- [Titkos kód beállítása és beolvasása Javával](../secrets/quick-create-java.md)
- [Titok beállítása és beolvasása Node.js](../secrets/quick-create-node.md)
- [Titkos kód beállítása és beolvasása .NET-tel (v4 SDK)](../secrets/quick-create-net.md)
- [Kulcstartó létrehozása és titkos kód hozzáadása Azure Resource Manager sablon használatával](../secrets/quick-create-template.md)

### <a name="set-and-retrieve-keys"></a>Kulcsok beállítása és lekérése

- [Kulcs beállítása és lekérése a parancssori felületről](../keys/quick-create-cli.md)
- [Kulcs beállítása és lekérése a PowerShell-lel](../keys/quick-create-powershell.md)
- [Kulcs beállítása és lekérése a Azure Portal](../keys/quick-create-portal.md)
- [A kulcsok működése REST-tel](/rest/api/keyvault/#key-operations)
- [Kulcs beállítása és lekérése a Pythonban](../secrets/quick-create-python.md)

### <a name="set-and-retrieve-certificates"></a>Tanúsítványok beállítása és lekérése
- [Tanúsítvány beállítása és lekérése parancssori felülettel](../certificates/quick-create-cli.md)
- [Tanúsítvány beállítása és lekérése a PowerShell-lel](../certificates/quick-create-powershell.md)
- [Tanúsítvány beállítása és lekérése a Azure Portal](../certificates/quick-create-portal.md)
- [Tanúsítvány-műveletek a REST-tel](/rest/api/keyvault/#certificate-operations)
- [Tanúsítvány beállítása és lekérése Python-val](../certificates/quick-create-python.md)

## <a name="coding-with-key-vault"></a>Kódolás Key Vault

A programozók Key Vault felügyeleti rendszere több felületet is tartalmaz. Ez a szakasz az összes nyelvre mutató hivatkozásokat, valamint néhány kódrészletet tartalmaz. 

### <a name="supported-programming-and-scripting-languages"></a>Támogatott programozási és programozási nyelvek

#### <a name="rest"></a>REST

Az összes Key Vault erőforrás a REST-felületen keresztül érhető el; tárolók, kulcsok, titkos kódok stb. 

[Key Vault REST API-hivatkozás](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[A Key Vault .NET API-referenciája](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

#### <a name="java"></a>Java

[Key Vault Java SDK](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

Node.js a Key Vault Management API és a Key Vault Object API különállóak. A következő áttekintő cikk a mindkettőhöz biztosít hozzáférést. 

[Node.jsAzure Key Vault moduljai ](https://docs.microsoft.com/javascript/api/overview/azure/key-vault-index?view=azure-node-latest)

#### <a name="python"></a>Python

[Azure Key Vault kódtárak a Pythonhoz](https://docs.microsoft.com/python/api/overview/azure/key-vault-index?view=azure-python)

#### <a name="azure-cli"></a>Azure CLI

[Azure CLI a Key Vaulthoz](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Key Vault Azure PowerShell](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Kódpéldák

A Key Vault alkalmazással való használatának teljes példáit a következő témakörben tekintheti meg:

- [Azure Key Vault kód mintái](https://azure.microsoft.com/resources/samples/?service=key-vault) – a Azure Key Vaulthoz tartozó példák. 

## <a name="how-tos"></a>Használati útmutatók

A következő cikkek és forgatókönyvek feladat-specifikus útmutatást nyújtanak a Azure Key Vault használatához:

- A [Key Vault-bérlő azonosítójának módosítása az előfizetés áthelyezése után](move-subscription.md) – ha áthelyezi az Azure-előfizetést az a bérlőtől a b bérlőhöz, a meglévő kulcstartók elérhetetlenné válnak a b bérlőhöz tartozó rendszerbiztonsági tag (felhasználók és alkalmazások) számára. javítsa ezt az útmutatót az útmutató használatával.
- A [tűzfal mögötti Key Vault elérése](access-behind-firewall.md) – egy kulcstartó eléréséhez a Key Vault-ügyfélalkalmazás számára a különböző funkciókhoz több végpontot is el kell érnie.
- [HSM-védelemmel ellátott kulcsok létrehozása és átvitele Azure Key Vaulthoz](../keys/hsm-protected-keys.md) – ez segít megtervezni, előállítani és továbbítani a saját HSM-védelemmel ellátott kulcsait, hogy azok a Azure Key Vault használatával legyenek használhatók.
- [Biztonságos értékek (például jelszavak)](../../azure-resource-manager/templates/key-vault-parameter.md) átadása az üzembe helyezés során – ha egy biztonságos értéket (például jelszót) kell megadni paraméterként az üzembe helyezés során, az értéket tárolhatja titkosként egy Azure Key Vault, és hivatkozhat az értékre más Resource Manager-sablonokban.
- A [Key Vault használata a bővíthető kulcsok felügyeletéhez a SQL Server használatával](https://msdn.microsoft.com/library/dn198405.aspx) – a SQL Server Connector Azure Key Vault lehetővé teszi, hogy a SQL Server és az SQL-a-a-VM kihasználja a Azure Key Vault szolgáltatást bővíthető kulcs-felügyeleti (EKM) szolgáltatóként, hogy megvédje a titkosítási kulcsait az alkalmazások hivatkozásához; Transzparens adattitkosítás, biztonsági másolatok titkosítása és az oszlopok szintjének titkosítása.
- [Tanúsítványok telepítése virtuális gépekre Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – az Azure-beli virtuális gépen futó Felhőbeli alkalmazásnak tanúsítványra van szüksége. Hogyan szerezheti be ezt a tanúsítványt még ma a virtuális gépre?
- Az [Azure webalkalmazás-tanúsítványnak a Key Vault segítségével történő üzembe helyezése]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) részletes útmutatást nyújt a Key Vault tárolt tanúsítványok [app Service-tanúsítvány](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) ajánlat részeként történő telepítéséhez.
- Hozzáférési szabályzat ([CLI](assign-access-policy-cli.md)  |  [PowerShell](assign-access-policy-powershell.md)  |  -[portál](assign-access-policy-portal.md)) társítása. A Key Vault akár 1024 hozzáférési szabályzatot is támogat. Ha továbbra is meg szeretné őrizni a korlátozást a felhasználók számára, hozzon létre Azure Active Directory biztonsági csoportokat, adja hozzá az összes társított egyszerű szolgáltatást a csoporthoz, majd adja meg a csoport számára a hozzáférést a Key Vaulthoz.
- A Key Vaultok Azure-beli integrálásával és használatával kapcsolatos további feladatra vonatkozó útmutatásért lásd: [Ryan Jones Azure Resource Manager sablon példák a Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- A [Key Vault Soft-delete használata a parancssori](soft-delete-cli.md) felülettel végigvezeti a kulcstartó használatának és életciklusának, valamint a helyreállítható törlést lehetővé tett különböző kulcstartó-objektumoknak a használatával.
- A Key Vault helyreállítható [Törlés használata a PowerShell-](soft-delete-powershell.md) lel végigvezeti a kulcstartó használatának és életciklusának, valamint a helyreállítható törlést lehetővé tett különböző kulcstartó-objektumoknak a használatával.

## <a name="integrated-with-key-vault"></a>Integrálva van Key Vault

Ezek a cikkek olyan egyéb forgatókönyvekkel és szolgáltatásokkal kapcsolatosak, amelyek a Key Vault-t használják vagy integrálják.

- [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md) kihasználja a Windows iparági szabvány [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) szolgáltatását és a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) szolgáltatását, hogy mennyiségi titkosítást biztosítson az operációs rendszer és az adatlemezek számára. A megoldás integrálva van Azure Key Vault, hogy segítsen a lemez titkosítási kulcsainak és a titkos kulcsoknak a kulcstartó-előfizetésben való felügyeletében és kezelésében, miközben biztosítja, hogy a virtuálisgép-lemezek összes adatai titkosítva legyenek az Azure Storage-ban.
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md) lehetőséget biztosít a fiókban tárolt adattitkosításhoz. A kulcskezelő szolgáltatásban a Data Lake Store két módot biztosít a fő titkosítási kulcsok (MEKs) kezelésére, amelyek a Data Lake Store tárolt adatok visszafejtéséhez szükségesek. Data Lake Store kezelheti a MEKs, vagy dönthet úgy, hogy megőrzi a MEKs tulajdonjogát a Azure Key Vault-fiók használatával. Data Lake Store fiók létrehozásakor megadhatja a kulcskezelő módot.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) lehetővé teszi a saját bérlői kulcsának felettesét. Például ahelyett, hogy a Microsoft a bérlői kulcsát (az alapértelmezettet) kezelhesse, a saját bérlői kulcsát úgy kezelheti, hogy megfeleljen a szervezetére vonatkozó egyes előírásoknak. A saját bérlőkulcs felügyelete más néven a saját kulcs használata (BYOK).

## <a name="key-vault-overviews-and-concepts"></a>Key Vault áttekintések és fogalmak

- [Key Vault-törlési viselkedés](soft-delete-overview.md)) egy olyan funkciót ismertet, amely lehetővé teszi a törölt objektumok helyreállítását, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e.
- [Key Vault az ügyfelek szabályozása](overview-throttling.md) a szabályozás alapvető fogalmait, és az alkalmazás megközelítését kínálja.
- [Key Vault biztonsági világok](overview-security-worlds.md) ismertetik a régiók és a biztonsági területek közötti kapcsolatokat.

## <a name="social"></a>Közösségi

- [Key Vault blog](https://aka.ms/kvblog)
- [Key Vault fórum](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Támogató kódtárak

- A [Microsoft Azure Key Vault Core Library](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) **rendszerállapotkulcsot** és **IKeyResolver** felületet biztosít a kulcsok azonosítókkal való megkereséséhez és a kulcsok használatával végzett műveletek végrehajtásához.
- A [Microsoft Azure Key Vault Extensions](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) a Azure Key Vault kibővített képességeit biztosítja.
