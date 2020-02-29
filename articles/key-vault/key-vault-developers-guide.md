---
title: Azure Key Vault fejlesztői útmutató
description: A fejlesztők a Azure Key Vault segítségével kezelhetik a titkosítási kulcsokat a Microsoft Azure környezetben.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 331e3d5855e4ea1fc18a3b638741f6297a7a27d8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184672"
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault fejlesztői útmutató

Key Vault lehetővé teszi a bizalmas adatok biztonságos elérését az alkalmazásokon belülről:

- A kulcsok és a titkos kulcsok védelme a kód írása nélkül történik, és az alkalmazások egyszerűen használhatók.
- Saját ügyfelei és saját kulcsaik is kezelhetők, így az alapszintű szoftverek funkcióinak biztosítására koncentrálhat. Így az alkalmazásai nem felelnek meg az ügyfelek bérlői kulcsainak és titkos kulcsainak.
- Az alkalmazás a kulcsokat az aláíráshoz és a titkosításhoz is használhatja, és az alkalmazáson kívül is tartja a kulcskezelő szolgáltatást, így a megoldás földrajzilag elosztott alkalmazásként is használható.
- A Key Vault szeptember 2016-es kiadásával az alkalmazásai már kezelhetik Key Vault tanúsítványokat. További információ: [a kulcsok, titkos kódok és tanúsítványok](/rest/api/keyvault/about-keys--secrets-and-certificates).

További általános információk a Azure Key Vaultről: [Mi az Key Vault](key-vault-overview.md).

## <a name="public-previews"></a>Nyilvános előzetes verziók

Időnként új Key Vault szolgáltatás nyilvános előzetes verzióját bocsátjuk rendelkezésére. Próbálja ki ezeket, és tudassa velünk, mit gondol a azurekeyvault@microsoft.comon keresztül, a visszajelzési e-mail-címével.

## <a name="creating-and-managing-key-vaults"></a>Kulcstartók létrehozása és kezelése

Az Azure Key Vault módot kínál a hitelesítő adatok, valamint egyéb kulcsok és titkos kódok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. Az Azure-erőforrások felügyelt identitásai megkönnyítik a probléma megoldását azáltal, hogy az Azure-szolgáltatások automatikusan felügyelt identitást biztosítanak Azure Active Directory (Azure AD). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia. 

Az Azure-erőforrások felügyelt identitásával kapcsolatos további információkért tekintse meg [a felügyelt identitások áttekintése](../active-directory/managed-identities-azure-resources/overview.md)című témakört. További információ a HRE használatáról: [alkalmazások integrálása a Azure Active Directorysal](../active-directory/develop/active-directory-integrating-applications.md).

A Key vaultban található kulcsok, titkok vagy tanúsítványok használata előtt a Key vaultot a CLI, a PowerShell, a Resource Manager-sablonok vagy a REST használatával hozhatja létre és kezelheti a következő cikkekben leírtak szerint:

- [Kulcstartók létrehozása és kezelése a CLI-vel](key-vault-manage-with-cli2.md)
- [Kulcstartók létrehozása és kezelése a PowerShell-lel](key-vault-overview.md)
- [Kulcstartó létrehozása és titkos kód hozzáadása Azure Resource Manager sablon használatával](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Kulcstartók létrehozása és kezelése a REST-tel](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Kódolás Key Vault

A programozók Key Vault felügyeleti rendszere több felületet is tartalmaz. Ez a szakasz az összes nyelvre mutató hivatkozásokat, valamint néhány kódrészletet tartalmaz. 

### <a name="supported-programming-and-scripting-languages"></a>Támogatott programozási és programozási nyelvek

#### <a name="rest"></a>REST

Az összes Key Vault erőforrás a REST-felületen keresztül érhető el; tárolók, kulcsok, titkos kódok stb. 

[Key Vault REST API-hivatkozás](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[A Key Vault .NET API-referenciája](/dotnet/api/microsoft.azure.keyvault).

A .NET SDK 2. x verziójával kapcsolatos további információkért tekintse meg a [kibocsátási megjegyzéseket](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Key Vault Java SDK](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

A Node. js-ben a Key Vault Management API és a Key Vault Object API különállóak. A következő áttekintő cikk a mindkettőhöz biztosít hozzáférést. 

[A Node. js Azure Key Vault moduljai](/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Azure Key Vault kódtárak a Pythonhoz](/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Azure CLI a Key Vaulthoz](/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Key Vault Azure PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

### <a name="quickstart-guides"></a>Gyors útmutatók

- [Key Vault létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [A Key Vault első lépései a Node. js-ben](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>Példák a kódokra

A Key Vault alkalmazással való használatának teljes példáit a következő témakörben tekintheti meg:

- [Azure Key Vault kód mintái](https://azure.microsoft.com/resources/samples/?service=key-vault) – a Azure Key Vaulthoz tartozó példák. 
- [Azure Key Vault használata webalkalmazásból](quick-create-net.md) – oktatóanyag, amelyből megtudhatja, hogyan használhatók a Azure Key Vault egy webalkalmazásból az Azure-ban. 

## <a name="how-tos"></a>Használati útmutatók

A következő cikkek és forgatókönyvek feladat-specifikus útmutatást nyújtanak a Azure Key Vault használatához:

- A [Key Vault-bérlő azonosítójának módosítása az előfizetés áthelyezése után](key-vault-subscription-move-fix.md) – ha áthelyezi az Azure-előfizetést az a bérlőtől a b bérlőhöz, a meglévő kulcstartók elérhetetlenné válnak a b bérlőhöz tartozó rendszerbiztonsági tag (felhasználók és alkalmazások) számára. javítsa ezt az útmutatót az útmutató használatával.
- A [tűzfal mögötti Key Vault elérése](key-vault-access-behind-firewall.md) – egy kulcstartó eléréséhez a Key Vault-ügyfélalkalmazás számára a különböző funkciókhoz több végpontot is el kell érnie.
- [HSM-védelemmel ellátott kulcsok létrehozása és átvitele Azure Key Vaulthoz](key-vault-hsm-protected-keys.md) – ez segít megtervezni, előállítani és továbbítani a saját HSM-védelemmel ellátott kulcsait, hogy azok a Azure Key Vault használatával legyenek használhatók.
- [Biztonságos értékek (például jelszavak)](../azure-resource-manager/templates/key-vault-parameter.md) átadása az üzembe helyezés során – ha egy biztonságos értéket (például jelszót) kell megadni paraméterként az üzembe helyezés során, az értéket tárolhatja titkosként egy Azure Key Vault, és hivatkozhat az értékre más Resource Manager-sablonokban.
- A [Key Vault használata a bővíthető kulcsok felügyeletéhez a SQL Server használatával](https://msdn.microsoft.com/library/dn198405.aspx) – a SQL Server Connector Azure Key Vault lehetővé teszi, hogy a SQL Server és az SQL-a-a-VM kihasználja a Azure Key Vault szolgáltatást bővíthető kulcs-felügyeleti (EKM) szolgáltatóként, hogy megvédje a titkosítási kulcsait az alkalmazások hivatkozásához; Transzparens adattitkosítás, biztonsági másolatok titkosítása és az oszlopok szintjének titkosítása.
- [Tanúsítványok telepítése virtuális gépekre Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – az Azure-beli virtuális gépen futó Felhőbeli alkalmazásnak tanúsítványra van szüksége. Hogyan szerezheti be ezt a tanúsítványt még ma a virtuális gépre?
- Az [Key Vault beállítása a végpontok közötti kulcsfontosságú rotációs és naplózási szolgáltatással](key-vault-key-rotation-log-monitoring.md) – ez a lépés végigvezeti a kulcsfontosságú rotációs és naplózási funkció beállításán Azure Key Vaultokkal.
- Az [Azure webalkalmazás-tanúsítványnak a Key Vault segítségével történő üzembe helyezése]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) részletes útmutatást nyújt a Key Vault tárolt tanúsítványok [app Service-tanúsítvány](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) ajánlat részeként történő telepítéséhez.
- [Engedélyek megadása számos alkalmazás számára a kulcstartó eléréséhez](key-vault-group-permissions-for-apps.md) Key Vault hozzáférés-vezérlési házirend legfeljebb 1024 bejegyzést támogat. Azonban létrehozhat egy Azure Active Directory biztonsági csoportot is. Adja hozzá az összes társított egyszerű szolgáltatást ehhez a biztonsági csoporthoz, majd engedélyezze a biztonsági csoport számára a Key Vaulthoz való hozzáférést.
- A Key Vaultok Azure-beli integrálásával és használatával kapcsolatos további feladatra vonatkozó útmutatásért lásd: [Ryan Jones Azure Resource Manager sablon példák a Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- A [Key Vault Soft-delete használata a parancssori](key-vault-soft-delete-cli.md) felülettel végigvezeti a kulcstartó használatának és életciklusának, valamint a helyreállítható törlést lehetővé tett különböző kulcstartó-objektumoknak a használatával.
- A Key Vault helyreállítható [Törlés használata a PowerShell-](key-vault-soft-delete-powershell.md) lel végigvezeti a kulcstartó használatának és életciklusának, valamint a helyreállítható törlést lehetővé tett különböző kulcstartó-objektumoknak a használatával.

## <a name="integrated-with-key-vault"></a>Integrálva van Key Vault

Ezek a cikkek olyan egyéb forgatókönyvekkel és szolgáltatásokkal kapcsolatosak, amelyek a Key Vault-t használják vagy integrálják.

- [Azure Disk Encryption](../security/fundamentals/encryption-overview.md) kihasználja a Windows iparági szabvány [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) szolgáltatását és a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) szolgáltatását, hogy mennyiségi titkosítást biztosítson az operációs rendszer és az adatlemezek számára. A megoldás integrálva van Azure Key Vault, hogy segítsen a lemez titkosítási kulcsainak és a titkos kulcsoknak a kulcstartó-előfizetésben való felügyeletében és kezelésében, miközben biztosítja, hogy a virtuálisgép-lemezek összes adatai titkosítva legyenek az Azure Storage-ban.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) lehetőséget biztosít a fiókban tárolt adattitkosításhoz. A kulcskezelő szolgáltatásban a Data Lake Store két módot biztosít a fő titkosítási kulcsok (MEKs) kezelésére, amelyek a Data Lake Store tárolt adatok visszafejtéséhez szükségesek. Data Lake Store kezelheti a MEKs, vagy dönthet úgy, hogy megőrzi a MEKs tulajdonjogát a Azure Key Vault-fiók használatával. Data Lake Store fiók létrehozásakor megadhatja a kulcskezelő módot.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) lehetővé teszi a saját bérlői kulcsának felettesét. Például ahelyett, hogy a Microsoft a bérlői kulcsát (az alapértelmezettet) kezelhesse, a saját bérlői kulcsát úgy kezelheti, hogy megfeleljen a szervezetére vonatkozó egyes előírásoknak. A saját bérlői kulcs kezelése más néven a saját kulcs használata vagy a BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Key Vault áttekintések és fogalmak

- Key Vault a helyreállítható [törlési viselkedés](key-vault-ovw-soft-delete.md) olyan funkciót ismertet, amely lehetővé teszi a törölt objektumok helyreállítását, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e.
- [Key Vault az ügyfelek szabályozása](key-vault-ovw-throttling.md) a szabályozás alapvető fogalmait, és az alkalmazás megközelítését kínálja.
- [Key Vault a Storage-fiók kulcsainak áttekintése](key-vault-ovw-storage-keys.md) az Azure Storage-fiókokhoz tartozó Key Vault-integrációs fiókok kulcsait ismerteti.
- [Key Vault biztonsági világok](key-vault-ovw-security-worlds.md) ismertetik a régiók és a biztonsági területek közötti kapcsolatokat.

## <a name="social"></a>Közösségi tartalom

- [Key Vault blog](https://aka.ms/kvblog)
- [Key Vault fórum](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Támogató kódtárak

- A [Microsoft Azure Key Vault Core Library](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) **rendszerállapotkulcsot** és **IKeyResolver** felületet biztosít a kulcsok azonosítókkal való megkereséséhez és a kulcsok használatával végzett műveletek végrehajtásához.
- A [Microsoft Azure Key Vault Extensions](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) a Azure Key Vault kibővített képességeit biztosítja.
