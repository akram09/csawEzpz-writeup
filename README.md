## Challenge Informations

In this challenge you are given a network socket connection and the script [challenge.py](./challenge.py) that you will communicate with , the description of the challenge is : **Crypto is hard : (wrap the flag in flag{...}) Author: negasora**   and the most interesting part is that this challenge has 400 points :man_shrugging:

## Explanation 

The main purpose in this challenge is to understand what the given script is doing , so when first  reading the script i saw that it is using the Elliptic Curve encryption with the help of the Sage Math library :jack_o_lantern:  .so if you are new to the elliptic curve signature here is a great tutorials that helped me :  [hackernoon post]( https://hackernoon.com/what-is-the-math-behind-elliptic-curve-cryptography-f61b25253da3 )  ,  [awesome meduim post ]( https://blog.goodaudience.com/very-basic-elliptic-curve-cryptography-16c4f6c349ed?gi=6bedbba6cb94 ) , and if you are not comfortable with the sage Math library then check out its [documentation]( http://doc.sagemath.org/ ) .

So the script is declaring a certain class named `Magic ` that contains a constructor method and 3 other methods : flag , encrypt and sign we will be explaining each method : 

* The  init function is creating an elliptic curve graph with the corresponding coefficient ( checkout this [doc]( http://doc.sagemath.org/html/en/reference/curves/sage/schemes/elliptic_curves/constructor.html ) ) the resulting graph has this equation : `y^2  = x^3 +117050x^2 + x` and is around the 2^221 - 3   , then we generate a random point P from the graph  and then we generate a random prime number as our private key  that will help us to create our public key that will be a the P `pubKey = point * private key` ( here we are not using the simple multiplication we are using the elliptic curve multiplication that means p*3  is equal to p+p+p ) so the result point is our pubKey .
* the encrypt function take a string message as an argument and  convert it to hex to get it int value then with the help of the elliptic curve module we can get the corresponding point of the graph by putting the x coordinate as our int value if it exist in the graph or it will throw exception if not , then we generate a random prime k to  create some credentials : `c = P * k ` ,  `cp = pubKey * k `  and `pm` is the point corresponding to the int value we got , and it will print the c point (x , y ,  z coordinates ) and the cp + pm coordinates .
* the sign function take as a parameter the coeffs table and the x , y of a point it verify that this point belong to the graph with the passed coeffs  and then it   calculate `priv * point `  and it print the result .
* the flag function get the flag and passed it to the encrypt function .

## How I solved It 

after well understanding the script and the elliptic curve algorithm , i concentrate on how to get the flag using the 3 commands so i started to create some equation :  if we put the flag command in the first so we will get this equation :  c = P * k , cp = pubKey * k   and the pm will be our flag point  the output will be c and cp+pm( we will call it res )  if you have clearly understand the script then you will notice from the init function that `pubKey = P *priv` so our equations will be like : `c = P * k ` `cp = P * priv * k ` so `cp = c * priv `  so if we can get cp from the c printed after the flag command we can do a simple subtraction ` pm = res- cp `  we can get the flag directly because the x coordinate of the pm is the int value of the flag  . so how can we achieve that ...... 

​                                                                            ![wait a minute !!!](wait_a_minute_meme.jpg)

after a long observation of the code i saw that the sign message take a point as an input and multiply it by the priv key so  finally we got  our solution : 

* So first we need to create the same elliptic curve graph  and then we pass the flag command to get the flag credentials   

  ```python
  coeffs = [0, 117050, 0, 1, 0]
  p = 2**221 - 3
  curve = EllipticCurve(GF(p), coeffs)
  r.recvuntil("> ")
  ## select the flag commande that will crypt the flag and send the crypt result
  r.sendline("flag")
  ## receive the crypted credentials
  ciphers_json  = r.recvline()
  ```

* Then we pass the c point to the sign method  to get the cp Point : 

  ```python
  r.sendline(json.dumps(ciphers_to_sign))
  ```

* And then we receive our signed credentials we do a subtraction and we got our flag convert to hex decode it to ascii and it will print :

  ```python
  flag_point  = flag_plus_pm_point - pm_point
  ## convert to hex and then to ascii
  flag = hex(int(flag_point[0]))[2:].decode("hex")
  ## print it
  print("flag{%s}" % flag)
  ```

   

