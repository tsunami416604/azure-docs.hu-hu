| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Erőforrások / [erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (erőforrás típusonkénti) |800 |Egy erőforrástípus változik |
| Az adott erőforráscsoport központi telepítések |800 |800 |
| Üzemelő példányonként források |800 |800 |
| Felügyeleti zárolásainak (egyedi hatókör) |20 |20 |
| (Egyes erőforrás vagy erőforráscsoport) címkék száma |15 |15 |
| Címke kulcs hossza |512 |512 |
| Címke érték hossza |256 |256 |


#### <a name="template-limits"></a>Sablon korlátok

| Érték | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Paraméterek |256 |256 |
| Változók |256 |256 |
| Erőforrások (például a példányszám) |800 |800 |
| kimenetek |64 |64 |
| Sablon kifejezés |24,576 karakter |24,576 karakter |
| Az exportált sablonok erőforrások |200 |200 | 
| Sablonok mérete |1 MB |1 MB |
| A paraméter mérete |64 KB |64 KB |

Néhány sablon korlátot azért lépheti túl a beágyazott sablon használatával. További információkért lásd: [kapcsolt sablonok használata az Azure-erőforrások telepítésekor](../articles/azure-resource-manager/resource-group-linked-templates.md). A paraméterek, változók vagy kimenetek számának csökkentése érdekében kombinálható egy objektum több értéket. További információkért lásd: [paraméterekként objektumok](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).