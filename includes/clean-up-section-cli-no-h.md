Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha várhatóan nincs szüksége ezekre az erőforrásokra a későbbiekben, az erőforráscsoport eltávolításával törölheti őket.
 
1. Az alábbi parancs futtatásával gondoskodhat arról, hogy az erőforráscsoportban ne legyenek mentendő erőforrások:

  ```azurecli
  az group show --name myResourceGroup
  ```

2. Ha törölné a megjelenített erőforrásokat, futtassa az alábbi parancsot:
 
  ```azurecli
  az group delete --name myResourceGroup
  ```
