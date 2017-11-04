---
title: "A fájl- és alkalmazásszinteken - Azure HDInsight felhasználói engedélyeinek kezelése |} Microsoft Docs"
description: "Hogyan kezelheti a fájlok és mappák engedélyeit a tartományhoz csatlakoztatott HDInsight-fürtök."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 9ca91721e691eca239478c4ac8b85e2652babdfd
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="manage-user-permissions-at-the-file-and-folder-levels"></a>A fájl- és alkalmazásszinteken felhasználói engedélyeinek kezelése

[A HDInsight-fürtök tartományhoz](./domain-joined/apache-domain-joined-introduction.md) Azure Active Directory (Azure AD) felhasználók erős hitelesítés használatára, továbbá *szerepköralapú hozzáférés-vezérlés* különböző szolgáltatások, például a YARN és a Hive (RBAC) házirendeket. Ha a fürt számára, az alapértelmezett adatokat tároló Azure Storage vagy a Windows Azure Storage Blobs (WASB), is kényszerítheti a fájl és mappa szintű engedélyek. Apache Pletyka segítségével szabályozhatja a fürt fájlokhoz való hozzáférést a szinkronizált az Azure AD-felhasználókat és csoportokat.
<!-- [synchronized Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md). -->

A HDInsight-fürtök tartományhoz az Apache Pletyka példány rendelkezik előre konfigurált a Pletyka-WASB szolgáltatással. A Pletyka-WASB szolgáltatás nem a felügyeleti házirendmotor Pletyka-HDFS, hasonló, de egy másik Pletyka hozzáférési házirendek kényszerítése. Pletyka-WASB szolgáltatási erőforrás a bejövő üzenet nem rendelkezik a megfelelő Pletyka házirend az alapértelmezett válasz esetén MEGTAGADÁS. A Pletyka szolgáltatás nem felel meg a WASB ellenőrzése.

## <a name="permission-and-policy-model"></a>Engedélyek és házirend-modell

Erőforrás-hozzáférési kérelmek értékeli ki a rendszer a következő folyamat használja:

![Apache Pletyka házirend értékelésének folyamata](./media/hdinsight-add-acls-at-file-folder-levels/ranger-policy-evaluation-flow.png)

MEGTAGADÁSI szabályokkal értékeli ki a rendszer először engedélyezési szabályokat követ. Végén megfelelő Ha nincsenek házirendek teljesül, a MEGTAGADÁS adja vissza.

### <a name="user-variable"></a>Felhasználói változó

Használhatja a `{USER}` változó, amikor egy felhasználó hozzáférhessen a szabályzatok hozzárendelését a `/{username}` alkönyvtárra, például:

```
resource: path=/app-logs/{USER}, user: {USER}, recursive=true, permissions: all, delegateAdmin=true
```

A fenti házirend hozzáférést biztosít a felhasználók a saját almappájában alatt a `/app-logs/` könyvtár. Ez a házirend néz a Pletyka felhasználói felületen:

![A felhasználói változó használatát – példa](./media/hdinsight-add-acls-at-file-folder-levels/user-variable.png)

### <a name="policy-model-examples"></a>Házirend modell példák

Az alábbi táblázat néhány példa a házirend-modell működését:

| Pletyka házirend | Meglévő fájlrendszer | Felhasználói-kérelem | eredménye |
| -- | -- | -- | -- |
| Regression/pénzügyi /, bob, írási | Regression | Belinszky, a fájl /data/finance/mydatafile.txt létrehozása | ENGEDÉLYEZÉSE – a "pénzügyi" jön létre, miatt elődje jelölőnégyzet köztes mappa |
| Regression/pénzügyi /, bob, írási | Regression | Alice, hozzon létre fájl /data/finance/mydatafile.txt | MEGTAGADÁS - nem a megfelelő házirend |
| / data/pénzügyi *, bob, írási | Regression | Belinszky, a fájl /data/finance/mydatafile.txt létrehozása | ENGEDÉLYEZÉSE – ebben az esetben a választható rekurzív házirend (`*`) jelen, tanulmányozza a [helyettesítő karaktereket](#wildcards) |
| /Data/Finance/mydatafile.txt, bob, írási | Regression | Belinszky, a fájl /data/finance/mydatafile.txt létrehozása | MEGTAGADÁS - elődje ellenőrzése "/ adatok" sikertelen lesz, mivel nincs házirend |
| /Data/Finance/mydatafile.txt, bob, írási | / data/pénzügyi | Belinszky, a fájl /data/finance/mydatafile.txt létrehozása | MEGTAGADÁS - elődelem, ellenőrizze a "/ data/pénzügyi" nincs szabályzat |

Engedélyek is szükségesek, hogy mappa vagy a fájlok szintjén, a művelet típusa alapján. "Olvasás/megnyitása" hívás például igényel a olvasási hozzáféréssel a fájl szintjén, a "create" hívás a elődje mappa szintjén engedélyekkel kell rendelkeznie.

### <a name="wildcards-"></a>Helyettesítő karaktereket (*)

Ha egy helyettesítő karakter (`*`) megtalálható a szabályzat elérési útja, a helyettesítő vonatkozik, az elérési útvonalat és az ahhoz tartozó teljes részfát. A rekurzió megegyezik a használatával egy `recurse-flag`. Pletyka-WASB a helyettesítő rekurzió és a részleges névegyeztetés jelez.

## <a name="manage-file-and-folder-level-permissions-with-apache-ranger"></a>Fájl- és mappaszintű engedélyeket Apache Pletyka kezelése

Ha még nem tette meg, kövesse az [ezeket az utasításokat](./domain-joined/apache-domain-joined-configure.md) tartományhoz tartozó új fürt létrehozásához.

Tallózással nyissa meg a Pletyka-WASB `https://<YOUR CLUSTER NAME>.azurehdinsight.net/ranger/`. Adja meg a fürt rendszergazdai jogosultságú felhasználónevet és jelszót, amelyet a fürt létrehozásakor megadott.

Történő bejelentkezés után a Pletyka irányítópult jelenik meg:

![Pletyka irányítópult](./media/hdinsight-add-acls-at-file-folder-levels/ranger-dashboard.png)

A fürt társított Azure Storage-fiókot az aktuális fájl- és mappaengedélyek megtekintéséhez kattintson a  ***CLUSTERNAME*_wasb** a WASB hozzáférés be hivatkozásra.

![Pletyka irányítópult](./media/hdinsight-add-acls-at-file-folder-levels/wasb-dashboard-link.png)

A jelenlegi házirend listája jelenik meg. Számos jellemző házirendek kiindulópontként szerepelnek – a részletekről az egyes házirendek példa módjait megjelenítéséhez.

Minden egyes házirend láthatja, hogy a házirend engedélyezve van-e, hogy naplózásra van konfigurálva, és bármely hozzárendelt csoportok és felhasználók. Két művelet gomb található egyes házirend: szerkesztése és törlése.

![Házirendek listája](./media/hdinsight-add-acls-at-file-folder-levels/policy-list.png)

### <a name="adding-a-new-policy"></a>Új szabályzat hozzáadása

1. A felső sarkában a WASB házirendek lapon válassza ki a **új házirend hozzáadása**.

    ![Új szabályzat hozzáadása](./media/hdinsight-add-acls-at-file-folder-levels/add-new.png)

2. Adjon meg egy leíró **házirendnév**. Adja meg az Azure **Tárfiók** a fürt (*fióknév*. blob.core.windows.net), és a **fiók tároló** létrehozásakor adott a fürt. Adja meg a **relatív elérési út** (képest a fürt) a használt mappa vagy fájl.

    ![Új házirend űrlap](./media/hdinsight-add-acls-at-file-folder-levels/new-policy.png)

3. Az űrlap alatt adja meg a **engedélyezése feltételek** az új erőforrás. Válassza ki a megfelelő csoportok és felhasználók, és a rájuk vonatkozó engedélyek beállítása. A következő példában azt még így lévő összes felhasználó számára a `sales` csoport olvasási/írási hozzáférést.

    ![Értékesítési engedélyezése](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales.png)

4. Kattintson a **Mentés** gombra.

### <a name="example-policy-conditions"></a>Példa házirend feltételei

Az Apache Pletyka [házirend kiértékelési folyamat](#permission-and-policy-model) megadható, hogy bármilyen kombinációját megadott engedélyezési, és a megtagadási feltételek az igényeinek. Íme, néhány példa:

1. Összes értékesítés felhasználó, de nincs műszerész engedélyezése:

    ![Lehetővé teszi az értékesítés, műszerész megtagadása](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns.png)

2. Összes értékesítés felhasználók, és a megtagadási összes műszerész, kivéve az internalizálási "hiveuser3" nevű, akik olvasási hozzáféréssel kell rendelkeznie:

    ![Lehetővé teszik az eladásokat, kivéve hiveuser3 műszerész megtagadása](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns-except-hiveuser3.png)

## <a name="next-steps"></a>Következő lépések

* [A tartományhoz csatlakoztatott HDInsight Hive-szabályzatok konfigurálása](./domain-joined/apache-domain-joined-run-hive.md)
* [Tartományhoz csatlakozó HDInsight-fürtök kezelése](./domain-joined/apache-domain-joined-manage.md)
* [Kezelése az Ambari,-Ambari felhasználók engedélyezése](hdinsight-authorize-users-to-ambari.md)

<!-- * [Synchronize Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md) -->

