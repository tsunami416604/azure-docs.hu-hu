---
title: Hozzon létre, és a egy Azure Database for MySQL-kiszolgáló konfigurálása az Ansible segítségével
description: Megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Database for MySQL-kiszolgálót az Ansible használatával
ms.service: ansible
keywords: ansible, azure, devops, bash, forgatókönyv, mysql, adatbázis
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: 85f3957af599c80c46871a126681d29dfa513431
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051016"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-by-using-ansible"></a>Hozzon létre, és a egy Azure Database for MySQL-kiszolgáló konfigurálása az Ansible segítségével
Az [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) egy felügyelt szolgáltatás, amellyel magas rendelkezésre állású MySQL-adatbázisokat futtathat, kezelhet és skálázhat a felhőben. Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. 

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy Azure Database for MySQL-kiszolgálót és konfigurálhatja annak tűzfalszabályát az Ansible használatával. Ezeket a feladatokat nagyjából öt perc alatt elvégezheti az Azure Portalon.

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Az oktatóanyagban szereplő következő forgatókönyvek futtatásához az Ansible 2.7-es verziója szükséges. 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.  

A következő példában létrehozunk egy **myResourceGroup** nevű erőforráscsoportot az **EastUS** helyen:

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

Mentse a fenti forgatókönyvet **rg.yml** néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>MySQL-kiszolgáló és -adatbázis létrehozása
A következő példa egy **mysqlserveransible** nevű MySQL-kiszolgálót és egy **mysqldbansible** nevű Azure Database for MySQL-példányt hoz létre. Ez egy alapszintű 5. generációs kiszolgáló egy virtuális maggal. 

A **mysqlserver_name** értékének egyedinek kell lennie. A régiónként és csomagonként elérhető érvényes értékek megismeréséhez tekintse meg a [tarifacsomagok dokumentációját](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Cserélje le a `<server_admin_password>` értéket egy jelszóra.

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

Mentse a fenti forgatókönyvet **mysql_create.yml** néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Tűzfalszabály konfigurálása
Egy kiszolgálószintű tűzfalszabály lehetővé teszi külső alkalmazások számára, hogy kapcsolódjanak a kiszolgálóhoz az Azure MySQL szolgáltatás tűzfalán keresztül. Ilyen külső alkalmazás például a **mysql** parancssori eszköz vagy a MySQL Workbench.
A következő példában egy olyan **externalaccess** nevű tűzfalszabályt hozunk létre, amely bármely külső IP-címről engedélyezi a csatlakozást. 

Adja meg a saját értékeit a **startIpAddress** és az **endIpAddress** paraméterhez. Használja a csatlakozási helyének megfelelő IP-címtartományt. 

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

> [!NOTE]
> A MySQL-hez készült Azure-adatbázis kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-as porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja az 3306-os portot.
> 

Itt az **azure_rm_resource** modullal lehet elvégezni ezt a feladatot. Ez a REST API közvetlen használatát teszi lehetővé.

Mentse a fenti forgatókönyvet **mysql_firewall.yml** néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-by-using-the-command-line-tool"></a>Csatlakozás a kiszolgálóhoz a parancssori eszközzel
[Letöltheti a MySQL-t](https://dev.mysql.com/downloads/) és telepítheti a számítógépére. Másik lehetőségként rákattinthat a kódmintákban található **Kipróbálás** gombra vagy az Azure Portal jobb felső eszköztárában található **>_** gombra is az **Azure Cloud Shell** megnyitásához.

Írja be a következő parancsokat: 

1. Csatlakozás a kiszolgálóhoz a **mysql** parancssori eszközzel:
```azurecli-interactive
 mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
```

2. A kiszolgáló állapotának megtekintése:
```sql
 mysql> status
```

Ha minden megfelelően működik, a parancssori eszköz a következő szöveget jeleníti meg:

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

## <a name="using-facts-to-query-mysql-servers"></a>Tények használata MySQL-kiszolgálók lekérdezéséhez
A következő példa lekérdezi a **myResourceGroup** erőforráscsoportban lévő MySQL-kiszolgálókat, és a kiszolgálókon lévő összes adatbázist:

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

Mentse a fenti forgatókönyvet **mysql_query.yml** néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook mysql_query.yml
```

Ezután a következő kimenet jelenik meg a MySQL-kiszolgálóhoz: 
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

Emellett a következő kimenet is megjelenik a MySQL-adatbázishoz:
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

Ha ezekre az erőforrásokra már nincs szüksége, a következő példa futtatásával törölheti őket. Ez töröl egy **myResourceGroup** nevű erőforráscsoportot. 

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

Mentse a fenti forgatókönyvet **rg_delete.yml** néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook rg_delete.yml
```

Ha csak az egyetlen újonnan létrehozott MySQL-kiszolgálót szeretné törölni, az alábbi példát futtassa:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Delete MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        state: absent
```

Mentse a fenti forgatókönyvet **mysql_delete.yml** néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Ansible az Azure-on](https://docs.microsoft.com/azure/ansible/)
