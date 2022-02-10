# Media Access Control
- permenanet
- physical
- unique

assigned by manufacturer
should be unique around the world

Changing the MAC address:

1. Bring the interface down
2. Change MAC address
3. Bring the interface up

`ifconfig eth0 down`
`ifconfig <interface> hw ether <new-mac-address>`
`ifconfig eth0 up`


