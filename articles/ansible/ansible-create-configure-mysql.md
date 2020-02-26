---
title: Oktatóanyag – adatbázisok konfigurálása Azure Database for MySQL Ansible használatával
description: Megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Database for MySQL-kiszolgálót az Ansible használatával
keywords: ansible, azure, devops, bash, forgatókönyv, mysql, adatbázis
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: f068b3022c94466a20b524240dc293392b1f42ff
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603126"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Oktatóanyag: adatbázisok konfigurálása Azure Database for MySQL Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

A [Azure Database for MySQL](/azure/mysql/overview) egy, a MySQL Community Edition-re épülő, kapcsolódó adatbázis-szolgáltatás. A Azure Database for MySQL lehetővé teszi a MySQL-adatbázisok kezelését a web Appsben.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * MySql-kiszolgáló létrehozása
> * MySql-adatbázis létrehozása
> * Tűzfalszabály konfigurálása úgy, hogy egy külső alkalmazás csatlakozhasson a kiszolgálóhoz
> * Kapcsolódás a MySql-kiszolgálóhoz az Azure Cloud shellből
> * A rendelkezésre álló MySQL-kiszolgálók lekérdezése
> * A csatlakoztatott kiszolgálókon lévő összes adatbázis listázása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az ebben a szakaszban található forgatókönyv-kód egy Azure-erőforráscsoportot hoz létre. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.  

Mentse a következő forgatókönyvet `rg.yml` néven:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:

* Létrejön egy `myResourceGroup` nevű erőforráscsoport.
* Az erőforráscsoport a `eastus` helyen jön létre:

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>MySQL-kiszolgáló és -adatbázis létrehozása

Az ebben a szakaszban található forgatókönyv-kód egy MySQL-kiszolgálót és egy Azure Database for MySQL példányt hoz létre. Az új MySQL-kiszolgáló egy 5. generációs alapszintű kiszolgáló egy virtuális mag, amelynek neve `mysqlserveransible`. Az adatbázis-példány neve `mysqldbansible`.

További információ a díjszabási csomagokról: [Azure Database for MySQL árképzési szintek](/azure/mysql/concepts-pricing-tiers). 

Mentse a következő forgatókönyvet `mysql_create.yml` néven:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:

* A `vars` szakaszban a `mysqlserver_name` értékének egyedinek kell lennie.
* A `vars` szakaszban cserélje le a `<server_admin_password>` jelszót.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Tűzfalszabály konfigurálása

A kiszolgálói szintű tűzfalszabály lehetővé teszi, hogy egy külső alkalmazás az Azure MySQL szolgáltatás tűzfala segítségével csatlakozhasson a kiszolgálóhoz. Külső alkalmazások például a `mysql` parancssori eszköz és a MySQL Workbench.

Az ebben a szakaszban szereplő forgatókönyv-kód egy `extenalaccess` nevű tűzfalszabály létrehozása, amely bármely külső IP-címről engedélyezi a kapcsolatokat. 

Mentse a következő forgatókönyvet `mysql_firewall.yml` néven:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:

* Az var szakaszban cserélje le a `startIpAddress` és a `endIpAddress`. Használja azon IP-címek tartományát, amelyek a csatlakozáshoz használt tartománynak felelnek meg.
* A MySQL-hez készült Azure-adatbázis kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-as porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja az 3306-os portot.
* A forgatókönyv a `azure_rm_resource` modult használja, amely lehetővé teszi a REST API közvetlen használatát.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Csatlakozás a kiszolgálóhoz

Ebben a szakaszban az Azure Cloud Shell használatával kapcsolódhat a korábban létrehozott kiszolgálóhoz.

1. Válassza ki a **TRY IT (kipróbálás** ) gombot a következő kódban:

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. A parancssorba írja be a következő parancsot a kiszolgáló állapotának lekérdezéséhez:

    ```sql
    mysql> status
    ```
    
    Ha minden megfelelően működik, a következő eredményekhez hasonló kimenet jelenik meg:
    
    ```
    demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65233
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
    
    Connection id:          65233
    Current database:
    Current user:           mysqladmin@13.76.42.93
    SSL:                    Cipher in use is AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.6.39.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 36 min 21 sec
    
    Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
    --------------
    ```
    
## <a name="query-mysql-servers"></a>MySQL-kiszolgálók lekérdezése

Az ebben a szakaszban szereplő forgatókönyv-kód a MySQL-kiszolgálókat kérdezi le `myResourceGroup` és felsorolja a talált kiszolgálók adatbázisait.

Mentse a következő forgatókönyvet `mysql_query.yml` néven:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook mysql_query.yml
```

A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

A MySQL-adatbázis következő kimenetét is megtekintheti:

```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Mentse a következő forgatókönyvet `cleanup.yml` néven:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)