#### get json object keys
```bash
jq 'keys'
``` 
#### We can use pipelines directly inside jq 
```bash
cat document.json | jq '.field[0] | keys'
```
#### number of keys/number of items in array, number of characters in string
```bash
jq '.field | length'
```
#### min max values of an array
```bash
echo '[1,2,3,4,5,6]' | jq 'min, max' 
```
#### reverse an array
```bash
echo '[1,2,3,4,5,5]' | jq 'reverse'
```
#### sort will sort array values
```bash
echo '[1,4,5,2,3]' | jq 'sort'
```
#### unique will sort and print unique values only
```bash
echo '[1,4,5,5,2,2,3]' | jq 'sort'
```
#### delete key
```bash
jq 'del(.field)'
```
#### join array items
```bash
echo '["hello", "world"]' jq 'join(" ")'
```
#### range
```bash
jq 'range(10)' <<< '{}'
jq 'range(1;10)' <<< '{}'
jq 'range(1;10;2)' <<< '{}'
```
#### has checks if json object has key or is array has item
```bash
jq 'has("key")'
echo '[1,2,3,4,5]' | jq 'has(3)'
```
#### map function is similar to jq '.[]' because it iterates through the array but the map function returns an array
```bash
jq 'map(.)'  
```
#### contains checks if value contains substring
```bash
jq '.field | contains("substring")'
```
#### select selects an object if criteria is met, and we can check for coditions using Comparison operators <, =<, ==, >= , >, != 
```bash
jq 'select(has("key"))' file #### print object if it has the required key
```
####for array iterations
```bash
jq 'map(select(.key == "value"))' file
```

#### env  access OS env variables using jq
```bash
jq -n 'env.ENV_VARIABLE'
```
####or
```bash
jq -n --arg x $USER '{"user": $x}' ####or for multiple variables jq -n --arg x $USER --arg y $HOSTNAME '{"user": $x, "host": $y}'
jq --arg key "name"  'map(has($key))' file.json
```

####Script example of this
```bash
!/bin/bash
export name=$(USER)
export dt=$(date)
jq -n '{"name": env.name,"date": env.dt}'
```
####Conditional statements

####bash script comparison of the same conditional
####IF condition
```bash
count=$1

if [[ ${count} -eq 100 ]] 
then
    echo 'Count is 100'
fi
```
####jq equivalent
```bash
jq -n --arg count $1 'select($count|tonumber == 100)| "Count is 100"'
```
####IF/Else condition
```bash
count=$1

if [[ ${count} -eq 100 ]] 
then
    echo 'Count is 100'
else 
    echo 'Count is not 100'
fi

jq -n --arg count $1 'if $count|tonumber == 100 then "Count is 100" else "Count is not 100" end'
```
####Exit status
####If you want an exit status then use the -e switch
```bash
jq -ne null ####will have the exit status 1
```

####Concatenate strings
```bash
jq -n '"Hello" + " " + "World"'
```
#### jq if
```bash
jq --arg $1 $PDN '.Reservations[].Instances[] | select(.PublicDnsName == $PDN)| .SecurityGroups[] | if .GroupName == "default" then .GroupId else  "" end'
```
#### jq multiple select options
```bash
jq '. | select(.Key | IN("one", "two"))'
```
