---
title: Az oktatóanyag - adatbázisok konfigurálása az Azure Database for MySQL-hez az Ansible-lel |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Database for MySQL-kiszolgálót az Ansible használatával
keywords: ansible, azure, devops, bash, forgatókönyv, mysql, adatbázis
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 7238e993ebd812734b3b08f57b7a4c2f080a7384
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764071"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Oktatóanyag: -Adatbázisok konfigurálása az Azure Database for MySQL-hez az Ansible-lel

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Database for MySQL](/azure/mysql/overview) relációsadatbázis-szolgáltatás alapján a MySQL közösségi kiadását. Azure Database for MySQL lehetővé teszi a webalkalmazások a MySQL-adatbázisok kezelését.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * MySql-kiszolgáló létrehozása
> * MySql-adatbázis létrehozása
> * Filewall szabály konfigurálása, hogy egy külső alkalmazás kapcsolódhat a kiszolgálóhoz
> * Csatlakozás a MySql-kiszolgáló a az Azure cloud shell
> * Az elérhető MySQL-kiszolgálók lekérdezéséhez
> * A csatlakoztatott kiszolgálókon található összes adatbázisok listája

## <a name="prerequisites"></a>Előfeltételek

* [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
* [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

A forgatókönyv kód ebben a szakaszban egy Azure-erőforráscsoportot hoz létre. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.  

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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:

* Nevű erőforráscsoport `myResourceGroup` jön létre.
* Az erőforrás-csoport ekkor létrejön a `eastus` helye:

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>MySQL-kiszolgáló és -adatbázis létrehozása

A forgatókönyv kód ebben a szakaszban létrehoz egy MySQL-kiszolgáló és a egy Azure Database for MySQL-példányt. Az új MySQL-kiszolgáló egy kiszolgáló egy virtuális mag Gen 5 alapszintű célú és nevű `mysqlserveransible`. Az adatbázis-példány neve `mysqldbansible`.

Díjszabással kapcsolatos további információkért lásd: [, Azure Database for MySQL tarifacsomagok](/azure/mysql/concepts-pricing-tiers). 

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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:

* Az a `vars` szakasz értékét `mysqlserver_name` egyedinek kell lennie.
* Az a `vars` szakaszban, cserélje le `<server_admin_password>` jelszóval.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Tűzfalszabály konfigurálása

Kiszolgálószintű tűzfalszabály lehetővé teszi, hogy egy külső alkalmazás szeretne csatlakozni a kiszolgálóhoz az Azure MySQL szolgáltatás tűzfalán keresztül. Néhány példa a külső alkalmazások a `mysql` parancssori eszköz és a MySQL Workbench segítségével.

A forgatókönyv kód ebben a szakaszban létrehoz egy tűzfalszabályt, nevű `extenalaccess` , amely lehetővé teszi, hogy a külső IP-címeket érkező kapcsolatokat. 

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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:

* Cserélje le a változók szakaszban `startIpAddress` és `endIpAddress`. Az IP-címtartományt, amelyek megfelelnek a tartomány, amelyből azt fogja kell csatlakozás használja.
* A MySQL-hez készült Azure-adatbázis kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-as porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja az 3306-os portot.
* A forgatókönyvet használja a `azure_rm_resource` modult, amely lehetővé teszi a REST API közvetlen használatát.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Csatlakozás a kiszolgálóhoz

Ebben a szakaszban az Azure cloud shell használhatja a korábban létrehozott kiszolgálóhoz való csatlakozáshoz.

1. Válassza ki a **Kipróbálom** gombra az alábbi kódot:

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. Amikor a rendszer kéri adja meg a következő parancsot a kiszolgáló állapotának lekérdezése:

    ```sql
    mysql> status
    ```
    
    Ha minden megfelelően működik, a következő eredményeket hasonló kimenet jelenik meg:
    
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
    
## <a name="query-mysql-servers"></a>Lekérdezés MySQL-kiszolgálók

Ebben a szakaszban a forgatókönyv kód lekéri a MySQL-kiszolgálók `myResourceGroup` és a kiszolgálókon található adatbázisok sorolja fel.

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook mysql_query.yml
```

A forgatókönyv futtatása után a következő eredményeket hasonló kimenet jelenik meg:

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

A MySQL-adatbázis a következő kimenetet is megjelenik:

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

Ha már nincs rá szükség, törölje az ebben a cikkben létrehozott erőforrásokat. 

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)