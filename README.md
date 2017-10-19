# UDPspeeder
Network Speed-Up Tool. Boost your Connection on a High Lantency High Packet-Loss Link by using Forward Error Correction.

When used alone,UDPspeeder speeds-up only udp connection.Nevertheless,if you used UDPspeeder + any UDP-based VPN together,you can speed-up any traffic(include tcp/udp/icmp)。

![](/images/en/udpspeeder.PNG)

or

![image_vpn](/images/en/udpspeeder+openvpn.PNG)

[简体中文](/doc/README.md)

# Efficacy
tested on a link with 100ms latency and 10% packet loss at both direction

### Ping Packet Loss
![](/images/en/ping_compare3.PNG)

### SCP Copy Speed
![](/images/en/scp_compare2.PNG)

# Supported Platforms
Linux host (including desktop Linux,Android phone/tablet,OpenWRT router,or Raspberry PI).

For Windows and MacOS You can run UDPspeeder inside [this](https://github.com/wangyu-/udp2raw-tunnel/releases/download/20170918.0/lede-17.01.2-x86_virtual_machine_image_with_udp2raw_pre_installed.zip) 7.5mb virtual machine image.

# How does it work

UDPspeeder uses FEC(Forward Error Correction) to improve your connection's quality,at the cost of addtional bandwidth.The algorithm for FEC is called Reed-Solomon.

### Reed-Solomon

`
In coding theory, the Reed–Solomon code belongs to the class of non-binary cyclic error-correcting codes. The Reed–Solomon code is based on univariate polynomials over finite fields.
`

`
It is able to detect and correct multiple symbol errors. By adding t check symbols to the data, a Reed–Solomon code can detect any combination of up to t erroneous symbols, or correct up to ⌊t/2⌋ symbols. As an erasure code, it can correct up to t known erasures, or it can detect and correct combinations of errors and erasures. Reed–Solomon codes are also suitable as multiple-burst bit-error correcting codes, since a sequence of b + 1 consecutive bit errors can affect at most two symbols of size b. The choice of t is up to the designer of the code, and may be selected within wide limits.
`

![](/images/en/rs.png)

Check wikipedia for more info, https://en.wikipedia.org/wiki/Reed–Solomon_error_correction

# Getting Started

### Installing
Download binary release from https://github.com/wangyu-/UDPspeeder/releases

### Running (speed-up UDP only)
Assume your server ip is 44.55.66.77, you have a service listening on udp port 7777.

```bash
# Run at server side:
./speederv2 -s -l0.0.0.0:4096 -r 127.0.0.1:7777  -f20:10

# Run at client side
./speederv2 -c -l0.0.0.0:3333  -r44.55.66.77:4096 -f20:10
```

Now connecting to UDP port 3333 at the client side is equivalent to connecting to port 7777 at the server side,and the connection is boosted by UDPspeeder.

##### Note

`-f20:10` means sending 10 redundant packets for every 20 original packets.

# Advanced Topic
### Full Options
```
UDPspeeder V2
git version:f479ca2779    build date:Oct 19 2017 01:37:08
repository: https://github.com/wangyu-/UDPspeeder

usage:
    run as client : ./this_program -c -l local_listen_ip:local_port -r server_ip:server_port  [options]
    run as server : ./this_program -s -l server_listen_ip:server_port -r remote_ip:remote_port  [options]

common option,must be same on both sides:
    -k,--key              <string>        key for simple xor encryption. if not set,xor is disabled
main options:
    -f,--fec              x:y             forward error correction,send y redundant packets for every x packets
    --timeout             <number>        how long could a packet be held in queue before doing fec,unit: ms
    --mode                <number>        fec-mode,available values: 0,1 ; 0 cost less bandwidth,1 cost less latency
    --report              <number>        turn on send/recv report,and set a period for reporting,unit:s
advanced options:
    --mtu                 <number>        mtu. for mode 0,the program will split packet to segment smaller than mtu_value.
                                          for mode 1,no packet will be split,the program just check if the mtu is exceed.
                                          default value:1250 
    -j,--jitter           <number>        simulated jitter.randomly delay first packet for 0~<number> ms,default value:0.
                                          do not use if you dont know what it means.
    -i,--interval         <number>        scatter each fec group to a interval of <number> ms,to protect burst packet loss.
                                          default value:0.do not use if you dont know what it means.
    --random-drop         <number>        simulate packet loss ,unit:0.01%. default value: 0
    --disable-obscure     <number>        disable obscure,to save a bit bandwidth and cpu
developer options:
    -j ,--jitter          jmin:jmax       similiar to -j above,but create jitter randomly between jmin and jmax
    -i,--interval         imin:imax       similiar to -i above,but scatter randomly between imin and imax
    -q,--queue-len        <number>        max fec queue len,only for mode 0
    --decode-buf          <number>        size of buffer of fec decoder,unit:packet,default:2000
    --fix-latency         <number>        try to stabilize latency,only for mode 0
    --delay-capacity      <number>        max number of delayed packets
    --disable-fec         <number>        completely disable fec,turn the program into a normal udp tunnel
    --sock-buf            <number>        buf size for socket,>=10 and <=10240,unit:kbyte,default:1024
log and help options:
    --log-level           <number>        0:never    1:fatal   2:error   3:warn 
                                          4:info (default)     5:debug   6:trace
    --log-position                        enable file name,function name,line number in log
    --disable-color                       disable log color
    -h,--help                             print this help message
```
### Speed-Up any traffic with OpenVPN+UDPspeeder

Check [UDPspeeder + openvpn config guide](/doc/udpspeeder_openvpn.md).