---
title: Azure Key Vault fejlesztői útmutató
description: A fejlesztők az Azure Key Vault segítségével kezelhetik a kriptográfiai kulcsokat a Microsoft Azure-környezetben.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: a8cb0ea9fb3c6e8388271c4274baf4ecc7282cda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247231"
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault fejlesztői útmutató

A Key Vault lehetővé teszi a bizalmas adatok biztonságos elérését az alkalmazásokból:

- Kulcsok és titkok védettanélkül, hogy írjon a kódot magad, és könnyen tudja használni őket az alkalmazások.
- Ön tudja, hogy az ügyfelek saját és kezelheti a saját kulcsokat, így koncentrálni, amely az alapvető szoftver funkciókat. Ily módon az alkalmazások nem a saját felelőssége vagy potenciális felelőssége az ügyfelek bérlői kulcsok és titkok.
- Az alkalmazás használhatja a kulcsokat az aláíráshoz és a titkosításhoz, de a kulcskezelést az alkalmazástól kívül tartja, lehetővé téve, hogy a megoldás földrajzilag elosztott alkalmazásként legyen megfelelő.
- A Key Vault-tanúsítványok kezelése. További információt a [Kulcsok, titkos kulcsok és tanúsítványok – további témakörökben talál.](about-keys-secrets-and-certificates.md)

Az Azure Key Vaultról további általános tudnivalókat a Mi a Key Vault című [témakörben talál.](key-vault-overview.md)

## <a name="public-previews"></a>Nyilvános előzetesek

Rendszeresidőközönként közzétesszük egy új Key Vault-funkció nyilvános előzetes verzióját. Próbálja ki ezeket, és tudassa azurekeyvault@microsoft.comvelünk, mit gondol keresztül, a visszajelzés e-mail címét.

## <a name="creating-and-managing-key-vaults"></a>Kulcstartók létrehozása és kezelése

Az Azure Key Vault módot kínál a hitelesítő adatok, valamint egyéb kulcsok és titkos kódok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. Az Azure-erőforrások felügyelt identitásai egyszerűbbé teszik a probléma megoldását azáltal, hogy az Azure-szolgáltatások nak automatikusan felügyelt identitást biztosít az Azure Active Directoryban (Azure AD). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia. 

Az Azure-erőforrások felügyelt identitásairól [a felügyelt identitások áttekintése című témakörben olvashat bővebben.](../active-directory/managed-identities-azure-resources/overview.md) Az AAD-vel való együttműködésről az [Alkalmazások integrálása](../active-directory/develop/active-directory-integrating-applications.md)az Azure Active Directoryval című témakörben talál további információt.

Mielőtt a kulcsokkal, titkos kulcsokkal vagy tanúsítványokkal dolgozna a kulcstartóban, létrehozza és kezeli a kulcstartót a CLI, a PowerShell, az Erőforrás-kezelő sablonok vagy a REST segítségével, az alábbi cikkekben leírtak szerint:

- [Kulcstartók létrehozása és kezelése CLI-vel](quick-create-cli.md)
- [Kulcstartók létrehozása és kezelése a PowerShell használatával](quick-create-powershell.md)
- [Kulcstárolók létrehozása és kezelése az Azure-porttal](quick-create-portal.md)
- [Kulcstárolók létrehozása és kezelése pythonnal](quick-create-python.md)
- [Kulcstartók létrehozása és kezelése Java-val](quick-create-java.md)
- [Kulcstartók létrehozása és kezelése a Node.js segítségével](quick-create-node.md)
- [Kulcstartók létrehozása és kezelése .NET (v4 SDK) segítségével](quick-create-net.md)
- [Hozzon létre egy kulcstartót, és adjon hozzá egy titkos kulcsot egy Azure Resource Manager-sablonon keresztül](quick-create-template.md)
- [Kulcstartók létrehozása és kezelése REST-el](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Kódolás a Key Vault segítségével

A programozók key vault-kezelő rendszere több felületből áll. Ez a rész az összes nyelvre mutató hivatkozásokat, valamint néhány kódpéldát tartalmaz. 

### <a name="supported-programming-and-scripting-languages"></a>Támogatott programozási és parancsfájlnyelvek

#### <a name="rest"></a>REST

A Key Vault összes erőforrása elérhető a REST-felületen keresztül; boltozat, kulcsok, titkok, stb. 

[Key Vault REST API-referencia](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[.NET API-hivatkozás a Key Vaulthoz](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

A .NET SDK 2.x verziójáról a [Kibocsátási megjegyzések című témakörben talál](key-vault-dotnet2api-release-notes.md)további információt.

#### <a name="java"></a>Java

[Java SDK a key vaulthoz](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

A Node.js,a Key Vault felügyeleti API és a Key Vault objektum API-t külön. A következő áttekintő cikk mindkettőhöz hozzáférést biztosít. 

[Azure Key Vault-modulok a Node.js-hez](/javascript/api/overview/azure/key-vault?view=azure-node-latest)

#### <a name="python"></a>Python

[Azure Key Vault-kódtárak pythonhoz](/python/api/overview/azure/key-vault?view=azure-python)

#### <a name="azure-cli"></a>Azure CLI

[Azure CLI a Key Vaulthoz](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell key vaulthoz](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Kódpéldák

A Key Vault alkalmazásokkal való használatának teljes példáit a következő témakörben talál:

- [Azure Key Vault-kódminták](https://azure.microsoft.com/resources/samples/?service=key-vault) – Az Azure Key Vault kódmintái. 
- [Használja az Azure Key Vault egy webalkalmazásból](quick-create-net.md) – oktatóanyag, amely segít megtanulni, hogyan használhatja az Azure Key Vault egy webes alkalmazás az Azure-ban. 

## <a name="how-tos"></a>Használati útmutatók

Az alábbi cikkek és forgatókönyvek feladatspecifikus útmutatást nyújtanak az Azure Key Vault használatával kapcsolatos munkához:

- [Kulcstároló bérlői azonosító módosítása az előfizetés áthelyezése után](key-vault-subscription-move-fix.md) – Amikor az Azure-előfizetést az A bérlőről a B bérlőre helyezi át, a meglévő kulcstartók nem érhetők el a bérlők (felhasználók és alkalmazások) számára a B bérlőben.
- [Key Vault tűzfal mögötti elérése](key-vault-access-behind-firewall.md) – A key vault ügyfélalkalmazás eléréséhez képesnek kell lennie a különböző funkciók több végponteléréséhez.
- [HSM-védelemmel ellátott kulcsok létrehozása és átvitele az Azure Key Vaulthoz](key-vault-hsm-protected-keys.md) – Ez segít megtervezni, generálni és átvinni saját HSM-védelemmel ellátott kulcsait az Azure Key Vault használatával.
- [Biztonságos értékek (például jelszavak) átvitele a központi telepítés során](../azure-resource-manager/templates/key-vault-parameter.md) – Ha egy biztonságos értéket (például egy jelszót) paraméterként kell átadnia a központi telepítés során, ezt az értéket titkos ként tárolhatja egy Azure Key Vaultban, és hivatkozhat az értékre más Resource Manager-sablonokban.
- [A Key Vault használata az SQL Server rel bővíthető kulcskezeléshez](https://msdn.microsoft.com/library/dn198405.aspx) – Az SQL Server Connector for Azure Key Vault lehetővé teszi az SQL Server és az SQL-in-a-VM használatát, hogy az Azure Key Vault szolgáltatást extensible key management (EKM) szolgáltatóként használják az alkalmazások titkosítási kulcsainak védelmére; Átlátszó adattitkosítás, biztonsági mentés titkosítása és oszlopszintű titkosítás.
- [Tanúsítványok üzembe helyezése a Key Vault virtuális gépeken](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – Az Azure-beli virtuális gépben futó felhőalkalmazásnak tanúsítványra van szüksége. Hogyan juthat be még ma ebbe a virtuális gépbe?
- [A Key Vault beállítása a kulcsrotáció és -naplózás végbe–](key-vault-key-rotation-log-monitoring.md) Ez bemutatja, hogyan állíthatja be a kulcsrotációt és a naplózást az Azure Key Vault használatával.
- [Az Azure Web App-tanúsítvány key vaulton keresztüli üzembe helyezése]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) lépésenként ismerteti a Key Vaultban tárolt tanúsítványok üzembe helyezését az [App Service-tanúsítványajánlat](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) részeként.
- [Engedély megadása számos alkalmazásnak a kulcstartó eléréséhez](key-vault-group-permissions-for-apps.md) A Key Vault hozzáférés-vezérlési házirendje legfeljebb 1024 bejegyzést támogat. Azonban létrehozhat egy Azure Active Directory biztonsági csoportot. Adja hozzá az összes társított egyszerű szolgáltatásnévit ehhez a biztonsági csoporthoz, majd adjon hozzáférést ehhez a biztonsági csoporthoz a Key Vault számára.
- A Key Vaultok Azure-beli integrálásával és használatával kapcsolatos további útmutatásért tekintse meg [a Ryan Jones Azure Resource Manager-sablonpéldáit a Key Vaulthoz.](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)
- [A Key Vault soft-delete használata a CLI-vel](key-vault-soft-delete-cli.md) végigvezeti a kulcstartó és a különböző kulcstartó-objektumok használatán és életciklusán, ha a soft-delete engedélyezve van.
- [A Key Vault soft-delete használata a PowerShell használatával](key-vault-soft-delete-powershell.md) végigvezeti a key vault és a különböző key vault-objektumok használatán és életciklusán, ha a soft-delete engedélyezve van.

## <a name="integrated-with-key-vault"></a>Integrálva a Key Vault-ba

Ezek a cikkek a Key Vaultot használó vagy a Key Vaultba integrálható egyéb forgatókönyvekről és szolgáltatásokról szólnak.

- [Az Azure Disk Encryption](../security/fundamentals/encryption-overview.md) a Windows szabványos [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) szolgáltatását és a Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcióját használja az operációs rendszer és az adatlemezek kötettitkosításának biztosításához. A megoldás integrálva van az Azure Key Vaultszolgáltatással, hogy segítsen a kulcstároló-előfizetés lemeztitkosítási kulcsainak és titkos kulcsainak vezérlésében és kezelésében, miközben biztosítja, hogy a virtuálisgép-lemezeken lévő összes adat inaktív módon titkosítva legyen az Azure-tárolóban.
- [Az Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) lehetőséget biztosít a fiókban tárolt adatok titkosításához. A kulcskezeléshez a Data Lake Store két módot biztosít a fő titkosítási kulcsok (MEKs) kezelésére, amelyek a Data Lake Store-ban tárolt adatok visszafejtéséhez szükségesek. A Data Lake Store kezelheti a MEK-ket, vagy dönthet úgy, hogy az Azure Key Vault-fiókjával megtartja a MEK-k tulajdonjogát. A Data Lake Store-fiók létrehozásakor adja meg a kulcskezelés módját.
- [Az Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) lehetővé teszi a saját bérlői kulcs kezelője. Például ahelyett, hogy a Microsoft kezelése a bérlői kulcs (az alapértelmezett), kezelheti a saját bérlői kulcsot, hogy megfeleljen a szervezetre vonatkozó előírásoknak. A saját bérlőkulcs felügyelete más néven a saját kulcs használata (BYOK).

## <a name="key-vault-overviews-and-concepts"></a>A Key Vault áttekintései és fogalmai

- [A Key Vault helyreállítható törlési viselkedése](key-vault-ovw-soft-delete.md) egy olyan szolgáltatást ír le, amely lehetővé teszi a törölt objektumok helyreállítását, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e.
- [Key Vault-ügyfél szabályozása](key-vault-ovw-throttling.md) irányítja Önt az alapvető fogalmak szabályozása, és egy megközelítést kínál az alkalmazáshoz.
- [Key Vault tárfiók kulcsok áttekintése](key-vault-ovw-storage-keys.md) ismerteti a Key Vault integrációs Azure Storage-fiókok kulcsok.
- [A Key Vault biztonsági világai](key-vault-ovw-security-worlds.md) a régiók és a biztonsági területek közötti kapcsolatokat ismertetik.

## <a name="social"></a>Közösségi tartalom

- [Key Vault Blog](https://aka.ms/kvblog)
- [Key Vault fórum](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Könyvtárak támogatása

- [A Microsoft Azure Key Vault Core Library](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) **IKey** és **IKeyResolver** felületeket biztosít az azonosítókból származó kulcsok megkereséséhez és a kulcsokkal végzett műveletek végrehajtásához.
- [A Microsoft Azure Key Vault-bővítmények](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) bővített lehetőségeket kínálnak az Azure Key Vault hoz.
