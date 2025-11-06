# The Magic 49(150 pts)


## Desc:

    The app speaks when spoken to, but only if you address it properly.

     connect to wifi-B&B, ip- 192.168.231.102 , port-2805


## Solution:

    The player tries the hint ?name={{ 7 * 7 }}. The page responds "Hello, 49!" This confirms a Server-Side Template Injection (SSTI) vulnerability. The server is using a template engine (like Flask/Jinja2) unsafely. The player then uses a known payload (e.g., ?name={{ config }}) to dump the server's configuration, which contains the secret flag.

## Flag

    bab{53RV3R_5ID3_T3MP1ATE_1NJ3CT10N}
