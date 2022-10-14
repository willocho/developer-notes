### Nested Type Inference

```
export declare type OmitFirst<T extends string[] | []> = T extends [] ? [] : T extends [string, ...infer R] ? R : [];

export declare type GetRootType<Type, Props extends [string, ...string[]] | [] = []> = 
Props extends [] 
    ? Type
    : Type extends object 
        ? Props[0] extends null | undefined 
            ? Type
            : Props[0] extends keyof Type 
                ? Props[1] extends keyof Type[Props[0]] 
                    ? GetRootType<Type[Props[0]], [Props[1], ...OmitFirst<OmitFirst<Props>>]> 
                    : never
                : never
        : Props[0] extends null | undefined ? Type : never

/**
 * Returns the value of an object by traversing the object tree. Unlike getValueByDotPath, this function will correctly infer
 * the type of the object that is returned
 */
export function getRoot<T, S extends [string, ...string[]] | [] = []>(objOrPrim: T, ...keys: S): GetRootType<T, S> {
    if(typeof objOrPrim === "object"){
        if(keys[0] && Object.keys(objOrPrim).includes(keys[0])){
            if(keys.length > 1 && typeof keys[1] === "string"){
                return getRoot(objOrPrim[keys[0] as keyof T], keys[1], ...keys.slice(2)) as GetRootType<T, S>;
            }
            else{
                return getRoot(objOrPrim[keys[0] as keyof T]) as GetRootType<T, S>;
            }
        }
        else if(keys.length === 0){
            return objOrPrim as GetRootType<T, S>;
        }
        else{
            throw new Error("Invalid keys passed. Keys: " + keys + "; Object: " + objOrPrim);
        }
    }
    else{
        if(keys.length === 0){
            return objOrPrim as GetRootType<T, S>;
        }
        else{
            throw new Error("Additional keys found. Keys: " + keys);
        }
    }
}
```
