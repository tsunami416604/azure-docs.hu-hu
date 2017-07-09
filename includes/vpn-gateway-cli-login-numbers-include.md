1. Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#login) paranccsal, és kövesse a képernyőn látható utasításokat. További információt a bejelentkezésről az [Azure CLI 2.0-s verzió használatának első lépéseit](/cli/azure/get-started-with-azure-cli) ismertető cikkben talál.

  ```azurecli
  az login
  ```
2. Ha több Azure-előfizetéssel rendelkezik, sorolja fel a fiókhoz tartozó előfizetéseket.

  ```azurecli
  az account list --all
  ```
3. Válassza ki a használni kívánt előfizetést.

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```