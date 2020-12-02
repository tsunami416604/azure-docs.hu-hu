---
title: Hozzáférés-vezérlés beállítása a szinapszis-munkaterülethez
description: Ez a cikk bemutatja, hogyan vezérelheti a szinapszis-munkaterülethez való hozzáférést az Azure-szerepkörök, a szinapszis-szerepkörök, az SQL-engedélyek és a git-engedélyek használatával.
services: synapse-analytics
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 7243d24204c8e15ae4246718cafb24d31f804d02
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96519178"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>Hozzáférés-vezérlés beállítása a szinapszis-munkaterülethez 

Ez a cikk bemutatja, hogyan vezérelheti a szinapszis-munkaterülethez való hozzáférést az Azure-szerepkörök, a szinapszis-szerepkörök, az SQL-engedélyek és a git-engedélyek használatával.   

Ebben az útmutatóban egy munkaterületet állít be, és egy alapszintű hozzáférés-vezérlési rendszer állítható be, amely számos szinapszis-projekt számára megfelelő.  Ezután részletesen leírja, hogy milyen speciális beállításokra van szükség.  

A szinapszis hozzáférés-vezérlését olyan biztonsági csoportok használatával lehet egyszerűsíteni, amelyek összhangban vannak a szervezet szerepköreivel és Personával.  A hozzáférés kezeléséhez csak a biztonsági csoportokból származó felhasználókat kell felvennie és eltávolítania.

Mielőtt elkezdené ezt a bemutatót, olvassa el a [szinapszis hozzáférés-vezérlésének áttekintése](./synapse-workspace-access-control-overview.md) című cikket, és ismerkedjen meg a szinapszis által használt hozzáférés-vezérlési mechanizmusokkal.   

## <a name="access-control-mechanisms"></a>Hozzáférés-vezérlési mechanizmusok

> [!NOTE]
> A jelen útmutatóban ismertetett megközelítés több biztonsági csoport létrehozása, majd szerepkörök kiosztása ezekhez a csoportokhoz. A csoportok beállítása után a munkaterület elérésének vezérléséhez csak a biztonsági csoportokon belüli tagságot kell kezelnie.

A szinapszis-munkaterület biztonságossá tételéhez kövesse az alábbi elemek konfigurálásának mintáját:

- **Biztonsági csoportok** a felhasználók hasonló hozzáférési követelményekkel való csoportosításához.
- **Azure-szerepkörök**, amelyek segítségével szabályozhatja, hogy ki hozhat létre és kezelhet SQL-készleteket, Apache Spark-készleteket és integrációs modulokat, és hogyan érheti el ADLS Gen2 tárolót
- **Szinapszis-szerepkörök**, a közzétett kódokhoz való hozzáférés szabályozása, Apache Spark számítási erőforrások és integrációs modulok használata 
- **SQL-engedélyek** a felügyeleti és az ADATsík SQL-készletekhez való hozzáférésének szabályozásához. 
- **Git-engedélyek**, amelyekkel szabályozhatja, hogy ki férhet hozzá a verziókövetés kódjához, ha a git-támogatást konfigurálja a munkaterülethez 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>A szinapszis-munkaterület biztonságossá tételének lépései

Ez a dokumentum szabványos neveket használ az utasítások egyszerűsítéséhez. Cserélje le őket a választott nevekre.

|Beállítás | Normál név | Leírás |
| :------ | :-------------- | :---------- |
| **Szinapszis-munkaterület** | `workspace1` |  A szinapszis munkaterület neve. |
| **ADLSGEN2-fiók** | `storage1` | A munkaterülethez használni kívánt ADLS-fiók. |
| **Tároló** | `container1` | A STG1 azon tárolója, amelyet a munkaterület alapértelmezés szerint fog használni. |
| **Active Directory-bérlő** | `contoso` | az Active Directory-bérlő neve.|
||||

## <a name="step-1-set-up-security-groups"></a>1. lépés: biztonsági csoportok beállítása

>[!Note] 
>Az előzetes verzió ideje alatt javasolt biztonsági csoportokat létrehozni a szinapszis **SZINAPSZIS SQL-rendszergazdához** és a **szinapszis Apache Spark rendszergazdai** szerepkörökhöz.  Az új, finomabb RBAC-szerepkörök és-hatókörök bevezetésével mostantól ajánlott ezeket az új képességeket használni a munkaterület elérésének szabályozásához.  Ezek az új szerepkörök és hatókörök nagyobb konfigurációs rugalmasságot biztosítanak, és felismerik, hogy a fejlesztők gyakran használják az SQL és a Spark kombinációját az elemzési alkalmazások létrehozásához, és előfordulhat, hogy hozzáférést kell biztosítani a munkaterületen lévő adott erőforrásokhoz. [További információk](./synapse-workspace-synapse-rbac.md).

Hozza létre a következő biztonsági csoportokat a munkaterülethez:

- **`workspace1_SynapseAdministrators`**, azon felhasználók számára, akiknek szükségük van a munkaterület feletti felügyeletre.  Adja hozzá magát ehhez a biztonsági csoporthoz, legalábbis kezdetben.
- **`workspace1_SynapseContributors`**, olyan fejlesztők számára, akiknek szükségük van kód a szolgáltatásba való fejlesztésére, hibakeresésére és közzétételére.   
- **`workspace1_SynapseComputeOperators`** azon felhasználók számára, akiknek Apache Spark-készleteket és integrációs modulokat kell kezelnie és figyelniük.
- **`workspace1_SynapseCredentialUsers`** azon felhasználók számára, akik a munkaterület MSI (felügyelt szolgáltatás identitása) hitelesítő adataival és a folyamat futtatásának megszakításával hibakeresési és futtatási folyamatokat igényelnek.   

A szinapszis-szerepköröket hamarosan a munkaterület hatókörében rendeli hozzá ezekhez a csoportokhoz.  

Hozza létre a következő biztonsági csoportot is: 
- **`workspace1_SQLAdministrators`**, azon felhasználók csoportja, akiknek szükségük van Active Directory rendszergazdai szolgáltatóra a munkaterület SQL-készletei között. 

A `workspace1_SynapseSQLAdministrators` rendszer akkor fogja használni a csoportot, ha az SQL-készletekben konfigurálja az SQL-engedélyeket a létrehozásuk során. 

Alapszintű beállítás esetén ez az öt csoport elegendő. Később hozzáadhat biztonsági csoportokat azon felhasználók kezeléséhez, akiknek több specializált hozzáférésre van szükségük, vagy csak adott erőforrásokhoz férnek hozzá a felhasználóknak.

> [!NOTE]
>- [Ebből a cikkből](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)megtudhatja, hogyan hozhat létre biztonsági csoportot ebben a cikkben.
>- [Ebből a cikkből](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal)megtudhatja, hogyan adhat hozzá biztonsági csoportot egy másik biztonsági csoportból ebben a cikkben.

>[!Tip]
>Az egyes szinapszis-felhasználók a Azure Portal Azure Active Directory segítségével megtekinthetik a csoporttagság alapján, hogy meghatározzák, mely szerepköröket adtak meg.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>2. lépés: a ADLS Gen2 Storage-fiók előkészítése

A szinapszis-munkaterület alapértelmezett tárolót használ a következőhöz:
  - A Spark-táblák biztonsági mentésére szolgáló adatfájlok tárolása
  - Végrehajtási naplók a Spark-feladatokhoz

Azonosítsa az alábbi adatokat a tárhelyéről:

- A munkaterülethez használni kívánt ADLS Gen2-fiók. Ez a dokumentum hívja meg `storage1` . `storage1` a munkaterület "elsődleges" Storage-fiókjának számít.
- Az a tároló `workspace1` , amelybe a szinapszis-munkaterület alapértelmezés szerint fog használni. Ez a dokumentum hívja meg `container1` . 

- A Azure Portal használatával rendelje hozzá a következő Azure-szerepköröket a `container1` biztonsági csoportokhoz 

  - Rendelje hozzá a **Storage blob adatközreműködői** szerepkört a következőhöz `workspace1_SynapseAdmins` 
  - Rendelje hozzá a **Storage blob adatközreműködői** szerepkört a következőhöz `workspace1_SynapseContributors`
  - Rendelje hozzá a **Storage blob adatközreműködői** szerepkört `workspace1_SynapseComputeOperators` **<< érvényesítéséhez**  

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>3. lépés: a szinapszis-munkaterület létrehozása és konfigurálása

A Azure Portal hozzon létre egy szinapszis-munkaterületet:

- Az előfizetés kiválasztása
- Válasszon ki vagy hozzon létre egy erőforráscsoportot, amelyhez az Azure **tulajdonosi** szerepköre van.
- A munkaterület elnevezése `workspace1`
- Válassza ki `storage1` a Storage-fiókot
- Válassza ki azt a `container1` tárolót, amelyet "fájlrendszerként" használ.
- WS1 megnyitása a szinapszis Studióban
- Navigáljon **Manage** a  >  **Access Control** kezelésére, és rendelje hozzá a következő szinapszis-szerepköröket a *munkaterület hatókörében* a biztonsági csoportokhoz.
  - A **szinapszis rendszergazda** szerepkörének kiosztása `workspace1_SynapseAdministrators` 
  - A **szinapszis közreműködő** szerepkörének kiosztása `workspace1_SynapseContributors` 
  - Rendelje hozzá a **SZINAPSZIS SQL számítási operátori** szerepkört a következőhöz `workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>4. lépés: a munkaterület MSI-hozzáférésének megadása az alapértelmezett tárolóhoz

A folyamatok futtatásához és a rendszerfeladatok végrehajtásához a szinapszis megköveteli, hogy a munkaterület által felügyelt szolgáltatás identitásának (MSI) hozzá kell férnie `container1` az alapértelmezett ADLS Gen2 fiókhoz.

- Nyissa meg az Azure Portalt
- Keresse meg a Storage-fiókot, `storage1` majd `container1`
- A **Access Control (iam)** használatával győződjön meg arról, hogy a **Storage blob-adatközreműködői** szerepkör hozzá van rendelve a munkaterület MSI-fájlhoz
  - Ha nincs hozzárendelve, rendelje hozzá.
  - Az MSI neve megegyezik a munkaterülettel. Ebben a cikkben a következő lenne: `workspace1` .

## <a name="step-5-grant-the-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>5. lépés: a szinapszis-rendszergazdák számára az Azure közreműködő szerepkör megadása a munkaterületen 

SQL-készletek, Apache Spark készletek és integrációs modulok létrehozásához a felhasználóknak legalább Azure közreműködői hozzáféréssel kell rendelkezniük a munkaterületen. A közreműködői szerepkör lehetővé teszi a felhasználók számára az erőforrások kezelését, beleértve a felfüggesztést és a skálázást is.

- Nyissa meg az Azure Portalt
- Keresse meg a munkaterületet, `workspace1`
- Rendelje hozzá az **Azure közreműködő** szerepkört a rendszerhez `workspace1` `workspace1_SynapseAdministrators` . 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>6. lépés: az SQL Active Directory rendszergazdai szerepkörének kiosztása

A munkaállomás létrehozója automatikusan Active Directory rendszergazdaként lesz beállítva a munkaterületen.  Csak egyetlen felhasználó vagy csoport kaphatja meg ezt a szerepkört. Ebben a lépésben hozzárendeli a Active Directory rendszergazdát a munkaterületen a `workspace1_SynapseSQLAdministrators` biztonsági csoporthoz.  A szerepkör hozzárendelésével ez a csoport magas jogosultsági szintű rendszergazdai hozzáférést biztosít az összes SQL-készlethez.   

- Nyissa meg az Azure Portalt
- Navigáljon ide: `workspace1`
- A **Beállítások** területen válassza az **SQL Active Directory-rendszergazda** elemet.
- Válassza a **rendszergazda beállítása** elemet, és válassza a **`workspace1_SynapseSQLAdministrators`**

>[!Note]
>Ez a lépés nem kötelező.  Dönthet úgy, hogy az SQL-rendszergazdák csoportnak egy kevésbé Kiemelt szerepkört ad. `db_owner`Ha vagy más SQL-szerepkört szeretne hozzárendelni, minden SQL-adatbázishoz parancsfájlokat kell futtatnia. 

## <a name="step-7-grant-access-to-sql-pools"></a>7. lépés: az SQL-készletek elérésének engedélyezése

Alapértelmezés szerint a szinapszis-rendszergazdai szerepkörrel rendelkező összes felhasználó az SQL-szerepkört is hozzárendeli a `db_owner` "beépített" kiszolgáló nélküli SQL-készlethez.

Az SQL-készletek más felhasználók számára való elérését és a munkaterület MSI-fájlját az SQL-engedélyek segítségével szabályozhatja.  Az SQL-engedélyek hozzárendeléséhez az szükséges, hogy az SQL-parancsfájlok az egyes SQL-készleteken fussanak a létrehozás után.  Három esetben kell futtatnia ezeket a parancsfájlokat:
1. Más felhasználók hozzáférésének biztosítása a "beépített" kiszolgáló nélküli SQL-készlethez
2. A dedikált készletekhez való felhasználói hozzáférés biztosítása
3. A munkaterület MSI-hozzáférésének engedélyezése egy SQL-készlethez az SQL-készlet elérését igénylő folyamatok sikeres futtatásához.

Az alábbi példa SQL-parancsfájlokat tartalmaz.

Ahhoz, hogy hozzáférést biztosítson egy dedikált SQL-készlethez, a szkripteket a munkaterület létrehozója vagy a csoport bármelyik tagja is futtathatja `workspace1_SynapseSQL Administrators` .  

A "beépített" kiszolgáló nélküli SQL-készlethez való hozzáférés biztosításához a szkripteket a csoport bármelyik tagja is futtathatja  `workspace1_SynapseAdministrators` . 

> [!TIP]
> Az alábbi lépéseket **minden** SQL-készlet esetében futtatni kell, hogy felhasználói hozzáférést biztosítson az összes SQL-adatbázishoz, kivéve a [munkaterület – hatókörrel rendelkező engedélyek](#workspace-scoped-permission) szakaszát, ahol a felhasználó rendszergazdai szerepkört rendelhet.

### <a name="step-71-serverless-sql-pools"></a>7,1. lépés: kiszolgáló nélküli SQL-készletek

Ebben a szakaszban példákat talál arra, hogy hogyan adhat engedélyt egy felhasználónak egy adott adatbázishoz vagy a teljes kiszolgálói engedélyekhez.

> [!NOTE]
> A parancsfájl példákban cserélje le az *aliast* a hozzáférést biztosító felhasználó vagy csoport aliasára, valamint a *tartományra* a használt vállalati tartománnyal.

#### <a name="pool-scoped-permission"></a>Készlet hatókörű engedélye

Ha **egyetlen** kiszolgáló nélküli SQL-készlethez szeretne hozzáférést adni egy felhasználónak, kövesse az alábbi példában szereplő lépéseket:

1. Bejelentkezés létrehozása

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. FELHASZNÁLÓ létrehozása

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. FELHASZNÁLÓ hozzáadása a megadott szerepkör tagjaihoz

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>Munkaterület – hatókörön belüli engedély

Ha teljes hozzáférést szeretne biztosítani a munkaterület **összes** kiszolgáló nélküli SQL-készletéhez, használja a következő példában szereplő parancsfájlt:

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>7,2. lépés: dedikált SQL-készletek

Ha **egyetlen** dedikált SQL-készlethez szeretne hozzáférést biztosítani, kövesse az alábbi lépéseket a szinapszis SQL Script Editorban:

1. Hozza létre a felhasználót az adatbázisban a következő parancs futtatásával a célként megadott adatbázison, majd válassza a *Kapcsolódás* legördülő lista használatával lehetőséget.

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Adja meg a felhasználónak az adatbázis eléréséhez szükséges szerepkört:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> a *db_datareader* és a *db_datawriter* írási/olvasási engedélyekkel is dolgozhat, ha nem kívánja megadni *db_owner* engedélyt.
> Ha egy Spark-felhasználónak közvetlenül a Sparkból vagy egy SQL-készletből kell olvasnia és írnia, *db_owner* engedélyre van szükség.

A felhasználók létrehozása után ellenőrizze, hogy a kiszolgáló nélküli SQL-készlet képes-e lekérdezni a Storage-fiókot.

### <a name="step-73-sl-access-control-for-workspace-pipeline-runs"></a>7,3. lépés: a munkaterület-folyamat futtatásának SL hozzáférés-vezérlése

### <a name="workspace-managed-identity"></a>Munkaterület által felügyelt identitás

> [!IMPORTANT]
> Ha olyan folyamatokat szeretne futtatni, amelyek olyan adatkészleteket vagy tevékenységeket tartalmaznak, amelyek SQL-készletre hivatkoznak, akkor a munkaterület identitásának hozzáférést kell biztosítania az SQL-készlethez.

Futtassa az alábbi parancsokat az egyes SQL-készleteken, hogy a munkaterület által felügyelt identitás az SQL-készlet adatbázisában futtasson folyamatokat:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Ez az engedély a következő parancsfájl ugyanazon SQL-készleten való futtatásával távolítható el:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="step-8-add-users-to-security-groups"></a>8. lépés: felhasználók hozzáadása biztonsági csoportokhoz

A hozzáférés-vezérlési rendszer kezdeti konfigurációja befejeződött.

A hozzáférés kezeléséhez hozzáadhat és eltávolíthat felhasználókat az Ön által beállított biztonsági csoportokhoz.  Bár a felhasználók manuálisan is hozzárendelhetők a szinapszis-szerepkörökhöz, ha igen, nem konfigurálja az engedélyeiket konzisztensen. Ehelyett csak felhasználókat adhat hozzá vagy távolíthat el a biztonsági csoportokhoz.

## <a name="step-9-network-security"></a>9. lépés: hálózati biztonság

A munkaterület biztonságossá tételének utolsó lépéseként biztonságossá kell tennie a hálózati hozzáférést a következők használatával:
- [Munkaterület tűzfala](./synapse-workspace-ip-firewall.md)
- [Felügyelt virtuális hálózat](./synapse-workspace-managed-vnet.md) 
- [Privát végpontok](./synapse-workspace-managed-private-endpoints.md)
- [Privát kapcsolat](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>10. lépés: Befejezés

A munkaterület mostantól teljesen konfigurálva van és biztonságos.

## <a name="supporting-more-advanced-scenarios"></a>Fejlettebb forgatókönyvek támogatása

Ez az útmutató egy alapszintű hozzáférés-vezérlési rendszer beállítására koncentrál. További speciális forgatókönyveket is támogathat, ha további biztonsági csoportokat hoz létre, és ezeket a csoportokat részletesebben szeretné hozzárendelni a pontosabb hatókörökhöz. Vegye figyelembe a következő eseteket:

A **git engedélyezése –** a munkaterület támogatása fejlettebb fejlesztési forgatókönyvekhez, például CI/CD-hez.  Git módban a git-engedélyek határozzák meg, hogy a felhasználó véglegesítheti-e a munkaágat.  A szolgáltatáshoz való közzététel csak az együttműködési ág alapján történik.  Érdemes lehet olyan biztonsági csoportot létrehozni a fejlesztőknek, akik egy működő ágban szeretnének fejlesztést és hibakeresést végezni, de nem kell közzétenniük az élő szolgáltatás módosításait.

A **fejlesztői hozzáférés korlátozása** adott erőforrásokhoz.  További finomabb biztonsági csoportokat hozhat létre azon fejlesztők számára, akiknek csak bizonyos erőforrásokhoz kell hozzáférni.  Ezek a csoportok megfelelő szinapszis-szerepköröket rendelnek hozzá, amelyek meghatározott Spark-készletekre, integrációs futtatókörnyezetekre vagy hitelesítő adatokra vannak korlátozva.

**Korlátozza a kezelők számára a kódok elérését**.  Hozzon létre biztonsági csoportokat azon operátorok számára, akiknek figyelniük kell a szinapszis számítási erőforrások működési állapotát, és meg kell tekintenie a naplókat, de nincs szükségük a kódok elérésére vagy a szolgáltatás frissítéseinek közzétételére. Rendelje hozzá ezeket a csoportokat a számítási operátori szerepkörhöz, amely meghatározott Spark-készletekre és integrációs modulokra terjed ki.  

## <a name="next-steps"></a>További lépések

Ismerje meg [, hogyan kezelheti a szinapszis-RBAC szerepkör-hozzárendeléseket](./how-to-manage-synapse-rbac-role-assignments.md) egy [szinapszis-munkaterület](../quickstart-create-workspace.md) létrehozásával
