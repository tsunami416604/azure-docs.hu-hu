
### <a name="cacheskuname"></a>cacheSKUName
Az új Azure Redis Cache tarifacsomagját.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Redis Cache."
      }
    },

A sablon határozza meg az értékeket, amelyeknél engedélyezve van ez a paraméter (Basic vagy Standard), és hozzárendeli az alapértelmezett értéket (alapszintű), ha nincs érték megadva. Basic biztosít egy egyetlen csomópont több másolatot elérhető méretek 53 GB.
Standard biztosít két csomópontos elsődleges vagy replika több másolatot elérhető méretek 53 GB és 99,9 %-os SLA-t.

### <a name="cacheskufamily"></a>cacheSKUFamily
A termékcsalád a termékváltozat.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
Az új Azure Redis Cache példány mérete. 

    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Redis Cache instance. "
      }
    }


A sablon határozza meg az értékeket, amelyeknél engedélyezve van ez a paraméter (0, 1, 2, 3, 4, 5 vagy 6), és hozzárendeli az alapértelmezett értéket (0), ha nincs érték megadva. A számok felel meg a következő gyorsítótár méretének: 0 = 250 MB, 1 = 1 GB-os, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

