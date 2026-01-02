1. 最大公因数( the Greatest Common Divisor, **GCD** )求法  
    辗转相除法/欧几里得算法: 2个数作除，不断把上一次的除数作为被除数、余数作为除数，直到2个数不再产生余数，此时的除数就是gcd
    ``` java
    public static long calculateGCD( long dividend, long divisor ){
        long remainder = dividend % divisor;
        while ( remainder > 0 ){
            dividend = divisor;
            divisor = remainder;
            remainder = dividend % divisor;
        }
        return divisor;
    }
    ```
   
2. 最小公倍数( the Least Common Multiple, **LCM** )求法  
    lcm = a/gcd(a,b) * b/gcd(a,b) * gcd(a,b) = ab/gcd(a,b)  
    2个数约分后作乘，再除以他们的最大公因数
    ``` java
    public static long calculateLCM( long a, long b ){
        return a * b / calculateGCD( a, b );
    }
   ```
