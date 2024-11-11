## Challenge: RSA Decryption with Elliptic Curve Method

### Problem Analysis
The challenge provided an RSA modulus `n`, an exponent `e`, and a ciphertext `cf`. The goal was to decrypt `cf` and retrieve the flag. The challenge hinted that standard methods might not work, so a more advanced approach was likely needed.

### Solution Approach

1. **Using RsaCtfTool**:
   - To try different RSA attack methods automatically, I used [RsaCtfTool](https://github.com/RsaCtfTool/RsaCtfTool.git).
   - I created an RSA public key in `.pem` format from the given `n` and `e` values and saved it as `Public.pem`.
   ```python
    from Cryptodome.PublicKey import RSA

    n = 24184405921426813952257404599844172064081900441874911388193652630310202789495631574938639097579036296101853234084915009375105829951961044132903535773924370570635332237832028283789889280165403481654234745557486403470274088268474644426048449169536944543970537467352336781804911800773427862282122961246703917704568734038884635778376198127691642199423912956685671445503565723497448770592525438558805325647407374824270703532081624469904632124745208505061145540656760946859657371270888296303072821867249045709759196878699068410554299707085233635566878012647656106156406503857113207
    e = 65537 
    cf = 19949070990331976245949375301773799373206800804362632168364316553540430616830618653790469820828363016574115997704094863829732182137092831141159044101049172034884217341208278135632024598752036879352726375804431415940381410379858267677290511438684691665260067686568252722840144131544524130614320199820713749708063642505970041091970221332182634285081892576008284361856437472575724015987128320812953565466004548726671456646020677256311338766802480861734450302201585275812208617677229147872618475814224684268204020352159322992065216537963716573003898076957096748095052514990199352 

    # Create an RSA key in PEM format
    key = RSA.construct((n, e))
    with open("Public.pem", "wb") as f:
        f.write(key.publickey().exportKey("PEM"))
    ```
2. **Installing SageMath**:
    - Since some advanced RSA techniques may require SageMath, I installed Sage using Conda. On a Kali Linux system where apt install is unavailable, I followed the [SageMath Conda installation guide](https://doc.sagemath.org/html/en/installation/conda.html).
3. **Running RsaCtfTool**:
    - I ran the following command to attempt decryption:
    ```bash
    python RsaCtfTool.py --publickey Public.pem --private --decryptfile Output.txt
    ```
    - RsaCtfTool reported an error but identified that the Elliptic Curve Method (ECM) was successful in factoring `n`.
4. **Using the Alpertron ECM Tool**:
    - With the ECM result from RsaCtfTool, I used the [Alpertron ECM](https://www.alpertron.com.ar/ECM.HTM) Tool to obtain Euler's totient `phi_n`.
5. **Decrypting the Message**:
    - With `phi_n` and the private exponent `d`, I used the following Python script to decrypt `cf` and retrieve the plaintext flag:
    ```python
    from Cryptodome.Util.number import getPrime, bytes_to_long, long_to_bytes
    import math

    n = 24184405921426813952257404599844172064081900441874911388193652630310202789495631574938639097579036296101853234084915009375105829951961044132903535773924370570635332237832028283789889280165403481654234745557486403470274088268474644426048449169536944543970537467352336781804911800773427862282122961246703917704568734038884635778376198127691642199423912956685671445503565723497448770592525438558805325647407374824270703532081624469904632124745208505061145540656760946859657371270888296303072821867249045709759196878699068410554299707085233635566878012647656106156406503857113207
    e = 65537 
    cf = 19949070990331976245949375301773799373206800804362632168364316553540430616830618653790469820828363016574115997704094863829732182137092831141159044101049172034884217341208278135632024598752036879352726375804431415940381410379858267677290511438684691665260067686568252722840144131544524130614320199820713749708063642505970041091970221332182634285081892576008284361856437472575724015987128320812953565466004548726671456646020677256311338766802480861734450302201585275812208617677229147872618475814224684268204020352159322992065216537963716573003898076957096748095052514990199352

    phi_n = 24184405921426813899393545717632914733985117059511028667244074356668539697980458319094855988399499600807882558726065448205575075349805062365078013112292907552145610038646202259319730773632094309783917924437504833077296614885148633344962972719486423126561887374070012771675267419501644175171798880802096023842502160929783163293738815036581810394203333529031482078535097296103636825918496767600434120921217534204388144423401717793953197670483357566342181544031900702549161201316253507936204548662203277722142730378180234338915740570559158753364879874873133377530101760000000000
    d = pow(e, -1, phi_n)

    # Decrypt the message
    pt = pow(cf, d, n)

    print(long_to_bytes(pt))
    ```

### Flag
`4T${sadly_this_is_too_much_prime_my_son}`