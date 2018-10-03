---
title: 'A saját kulcs használata: az Apache Kafka az Azure HDInsight (előzetes verzió)'
description: Ez a cikk ismerteti az Azure Key vaultból saját kulcs használata, az Apache Kafka Azure HDInsight platformon tárolt adatok titkosításához.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 85fea195b05bea8a1db70f8b5b81cabdfe7c6c72
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041509"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>A saját kulcs használata: az Apache Kafka az Azure HDInsight (előzetes verzió)

Az Azure HDInsight az Apache Kafka Bring Your Own Key (BYOK) támogatását tartalmazza. Ez a funkció lehetővé teszi, hogy Ön a tulajdonosa, és az inaktív adatok titkosításához használt kulcsok kezelése. 

Azok a felügyelt lemezek HDInsight védve vannak az Azure Storage Service Encryption (SSE). Alapértelmezés szerint az adatokat a lemezeken lévő van titkosítva, a Microsoft által kezelt kulcsok használata. Ha engedélyezi a BYOK, meg kell adnia a HDInsight használata és kezelése az Azure Key Vault használatával való titkosítására szolgáló kulcsot. 

A BYOK-titkosítás a további költségek nélkül a fürt létrehozásakor kezelt adatbázisunk. Teendők csak HDInsight regisztrálása az Azure Key Vault egy felügyelt identitás, és adja hozzá a titkosítási kulcsot, a fürt létrehozásakor.

A Kafka-fürt (beleértve a Kafka által kezelt replikák) azokat az üzeneteket az egy szimmetrikus adatok titkosítási kulcsa (Adattitkosítási) vannak titkosítva. Az adattitkosítási kulcsot a kulcs titkosítása kulcscserekulcs (KEK) a key vaultból használatával védett. A titkosítási és visszafejtési folyamatok teljes egészében az Azure HDInsight a kezeli. 

Az Azure Portalon vagy az Azure CLI segítségével biztonságosan elforgatása a kulcsok a key vaultban. Kulcs forgatása, a HDInsight Kafka-fürt elindítja a percen belül az új kulccsal. Engedélyezze a "Nem kiürítése" és "A helyreállítható törlés" Kulcsvédelmi szolgáltatásokat zsarolóprogram-forgatókönyvek és véletlen törlés elleni védelem érdekében. Védelmi szolgáltatások nélkül kulcsok nem támogatottak.

## <a name="get-started-with-byok"></a>A BYOK használatának első lépései

1. Hozzon létre felügyelt identitások az Azure-erőforrásokhoz.

   A hitelesítést a Key Vault, hozzon létre egy felügyelt identitás felhasználó által hozzárendelt a [az Azure Portal](../../active-directory/managed-service-identity/how-to-manage-ua-identity-portal.md), [Azure PowerShell-lel](../../active-directory/managed-service-identity/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-service-identity/how-to-manage-ua-identity-arm.md), vagy [ Az Azure CLI](../../active-directory/managed-service-identity/how-to-manage-ua-identity-cli.md). Míg az Azure Active Directoryval felügyelt identitások és a kafka BYOK, vállalati biztonsági csomag (ESP) nem követelmény. Győződjön meg arról, a felügyelt identitás erőforrás-azonosító mentése a Key Vault hozzáférési szabályzattal való hozzáadásakor.

   ![Felhasználó által hozzárendelt felügyelt identitás létrehozása az Azure Portalon](./media/apache-kafka-byok/user-managed-identity-portal.png)

2. Egy meglévő kulcstárolóba importálja, vagy hozzon létre egy újat.

   HDInsight csak az Azure Key Vault támogatja. Ha rendelkezik saját key vault, a kulcsok importálhatja az Azure Key Vaultban. Ne feledje, hogy a kulcsok "A helyreállítható törlés" és "Tegye nem végleges törlése" engedélyezve kell-e. Az "A helyreállítható törlés" és "Nem kiürítése" funkciók érhetők el a REST, .NET-en keresztül / C#, a PowerShell és az Azure CLI-felületeihez.

   Hozzon létre egy új kulcstartót, hajtsa végre a [Azure Key Vault](../../key-vault/key-vault-get-started.md) rövid. Meglévő kulcsok importálása kapcsolatos további információkért látogasson el [kapcsolatos kulcsok, titkos kódok és tanúsítványok](../../key-vault/about-keys-secrets-and-certificates.md).

   Hozzon létre egy új kulcsot, jelölje be **létrehozás/importálás** származó a **kulcsok** menüt **beállítások**.

   ![Hozzon létre egy új kulcsot az Azure Key Vaultban](./media/apache-kafka-byok/kafka-create-new-key.png)

   Állítsa be **beállítások** való **Generate** , és adja meg a kulcs nevét.

   ![Hozzon létre egy új kulcsot az Azure Key Vaultban](./media/apache-kafka-byok/kafka-create-a-key.png)

   Válassza ki a listából, kulcsok létrehozott kulcsot.

   ![Az Azure Key Vault-kulcs listája](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

   Ha a Kafka-fürt titkosítási saját kulcsot használ, meg kell adnia a kulcs URI-t. Másolás a **azonosítója** és helyre mentse, amíg készen áll a fürt létrehozásához.

   ![Másolja a kulcs azonosítója](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
3. Adja hozzá a key vault hozzáférési szabályzattal felügyelt identitás.

   Hozzon létre egy új Azure Key Vault hozzáférési szabályzattal.

   ![Új Azure Key Vault hozzáférési szabályzat létrehozása](./media/apache-kafka-byok/add-key-vault-access-policy.png)

   A **rendszerbiztonsági tag kijelölése**, válassza ki a létrehozott felügyelt felhasználó által hozzárendelt identitások.

   ![Az Azure Key Vault hozzáférési házirend beállítása rendszerbiztonsági tag kijelölése](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

   Állítsa be **Kulcsengedélyek** való **első**, **kulcs kicsomagolása**, és **kulcs becsomagolása**.

   ![Az Azure Key Vault hozzáférési szabályzattal kulcs engedélyeinek beállítása](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

   Állítsa be **titkos kód engedélyei** való **első**, **beállítása**, és **törlése**.

   ![Az Azure Key Vault hozzáférési szabályzattal kulcs engedélyeinek beállítása](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

4. HDInsight-fürt létrehozása

   Most már készen áll egy új HDInsight-fürt létrehozásához. A BYOK csak akkor alkalmazható új fürtök fürt létrehozása során. Titkosítási BYOK fürtből nem távolítható el, és a BYOK nem adható hozzá meglévő fürtök.

   ![Lemeztitkosítás Kafka az Azure Portalon](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   Fürt létrehozása során, adja meg az összes kulcs URL-CÍMÉT, a kulcs verzióját is beleértve. Például: `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Is kell rendelni a felügyelt identitás a fürthöz, és adja meg a kulcs URI-t.

## <a name="faq-for-byok-to-kafka"></a>A BYOK kafka – gyakori kérdések

**Hogyan a Kafka-fürt a kulcstartó elérését?**

   A HDInsight Kafka-fürt a fürt létrehozásakor egy felügyelt identitás társítása. A felügyelt identitást is létrehozható, mielőtt vagy a fürt létrehozása során. Emellett szüksége felügyelt identitás hozzáférést a kulcstartóhoz, a kulcs tárolására.

**Ez a funkció érhető el minden, a HDInsight Kafka-fürtök?**

   A BYOK-titkosítás csak akkor lehetséges, a Kafka 1.1-es és újabb fürtök.

**Használhatok különböző témakörök/partíciók különböző kulcsokat?**

   Nem, a fürt összes felügyelt lemezek titkosított ugyanazzal a kulccsal.

**Hogyan tudok helyreállítani a fürtöt, ha a kulcsokat a rendszer törli?**

   Csak a "A helyreállítható törlés" engedélyezett kulcsok támogatottak, ha a kulcsok vissza vannak állítva, a key vaultban, mivel a fürt anyagaihoz kell a kulcsokat. Az Azure Key Vault-kulcs visszaállítása, lásd: [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey).

**Lehetnek-e a BYOK és a nem a BYOK fürtön egyszerre használata előállítói/fogyasztói alkalmazások?**

   Igen. A BYOK használata transzparens előállítói/fogyasztói alkalmazások számára. Titkosítás az operációs rendszer rétegben történik. Nem szükséges módosításokat kell tenni a létező előállítói/fogyasztói Kafka alkalmazásokat.

**Operációsrendszer-lemez és az erőforrások lemezek is titkosítva van?**

   Nem. Operációsrendszer- és erőforrás-lemezek nem titkosítottak.

**Ha a fürt vertikális felskálázása esetén az új közvetítők támogatni fogja a BYOK zökkenőmentesen?**

   Igen. A fürt hozzá kell férnie a kulcsot a kulcstartóban a méretezés során fel. Ugyanazt a kulcsot a fürt minden felügyelt lemezen titkosítására szolgál.

**A BYOK érhető el a tartózkodási hely?**

   A nyilvános felhők Kafka BYOK érhető el.

## <a name="next-steps"></a>További lépések

* Azure Key Vaulttal kapcsolatos további információkért lásd: [Mi az Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* Ismerkedés az Azure Key Vault, lásd: [Ismerkedés az Azure Key Vault](../../key-vault/key-vault-get-started.md).
