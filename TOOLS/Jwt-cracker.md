- `jwt-cracker -t <token> [-a <alphabet>] [--max <maxLength>] [-d <dictionaryFilePath>] [-f]`
```
    token: the full HS256-512 JWT token string to crack
    alphabet: the alphabet to use for the brute force (default: "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789")
    maxLength: the max length of the string generated during the brute force (default: 12)
    dictionaryFilePath: path to a list of passwords (one per line) to use instead of brute force
    force: force script to execute when the token isn't valid
```
- `jwt-cracker -t eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ -a abcdefghijklmnopqrstuwxyz --max 6`
- `jwt-cracker -t eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ -d darkweb2017-top10000.txt`
