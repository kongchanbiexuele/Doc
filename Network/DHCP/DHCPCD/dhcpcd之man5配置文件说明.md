# dhcpcd之man5配置文件说明

## 简介

> 文件名：dhcpcd.conf[dhcpcd标准配置文件]

### 描述

Although dhcpcd can do everything from the command line, there are cases where it's just easier to do it once in a configuration file.  Most of the options found in dhcpcd(8) can be used here.  The first word on the line is the option and the rest of the line is the value.  Leading and trailing whitespace for the option and value are trimmed.  You can escape characters in the value using the \ character.  Comments can be prefixed with the # character.  String values should be quoted with the " character.

尽管dhcpcd可以从命令行执行所有操作，但在某些情况下，在配置文件中执行一次操作会更容易。dhcpcd（8）中的大多数选项都可以在这里使用。每一行的第一个单词是选项，行的其余部分是值。选项和值的前缀和后缀空白将被修剪。您可以使用\字符对值中的字符进行转义。注释可以以#字符为前缀。字符串值应该用“”字符引起来。

## 支持的options

### allowinterfaces pattern[接口白名单]

When discovering interfaces, the interface name must match pattern which is a space or comma separated list of patterns passed to fnmatch(3).  If the same interface is matched in denyinterfaces then it is still denied.

当发现接口时，接口名称必须与模式匹配，该模式是传递给fnmatch（3）的模式的空格或逗号分隔列表。如果同一个接口在拒绝接口中匹配，那么它仍然被拒绝。

### denyinterfaces pattern[接口黑名单]

When discovering interfaces, the interface name must not match pattern which is a space or comma separated list of patterns passed to fnmatch(3).

当发现接口时，接口名称必须与传递给fnmatch（3）的模式列表中以空格或逗号分隔的模式不匹配。

### anonymous[匿名模式]

Enables Anonymity Profiles for DHCP, RFC 7844.  Any DUID is ignored and ClientID is set to LL only.  All non essential options are then masked at this point, but they could be unmasked by explicitly requesting the option after the anonymous option is processed.  As such, the anonymous option should be the last option in the configuration unless you really want to send something which could identify you. dhcpcd will not try and reboot an old lease, it will go straight into DISCOVER/SOLICIT.

根据[RFC 7844](https://www.rfc-editor.org/rfc/rfc7844)协议，为DHCP启用匿名配置文件。将忽略任何DUID，并且ClientID仅设置为LL。此时，所有非必要选项都会被屏蔽，但在处理匿名选项后，可以通过显式请求该选项来取消屏蔽。因此，匿名选项应该是配置中的最后一个选项，除非您真的想发送可以识别您身份的内容。dhcpcd不会尝试重新启动旧租约，它将直接进入DISCOVER/SOLICIT。

【解读：匿名模式会根据最小配置去为接口完成dhcp。其他显式的配置会将匿名模式配置覆盖。匿名模式配置应该被放在最后。（以符合它优先级最低的策略）】

### randomise_hwaddr [随机硬件地址]

Forces a hardware address randomisation when the interface is brought up or when the carrier is lost.  This is generally used in tandem with the anonymous option.

当接口启动或载波丢失时，强制硬件地址随机化。这通常与匿名选项一起使用。

【解读：强制生成随机的硬件mac地址对外显示，以隐藏本地实际的mac地址】

### arping [address] [ARPING]

### authprotocol [algorithm [rdm]] [身份验证协议]
Authenticate DHCP messages. See the Supported Authentication Protocols section.  If protocol is token then algorithm is snd_secretid/rcv_secretid so you can send and receive different tokens.

对DHCP消息进行身份验证，配置不同的认证协议。请参阅支持的身份验证协议部分。若协议是令牌，那么算法是snd_secretid/rcv_secretid，所以你们可以发送和接收不同的令牌。

### authtoken [secretid] [realm] [expire] [key]

Define a shared key for use in authentication.  realm can be "" to for use with the delayed protocol.  expire is the date the token expires and should be formatted "yyy-mm-dd HH:MM".  You can use the keyword forever or 0 which means the token never expires.  For the token protocol, secretid needs to be 0 and realm needs to be "".  If dhcpcd has the error dhcp_auth_encode: Invalid argument then it means that dhcpcd could not find the correct authentication token in your configuration.

定义用于身份验证的共享密钥。realm可以是“”到，以便与延迟协议一起使用。expire是令牌到期的日期，格式应为“yyy mm-dd HH:mm”。您可以永远使用关键字或0，这意味着令牌永远不会过期。对于令牌协议，secretid需要为0，realm需要为“”。如果dhcpcd有错误dhcp_auth_encode:Invalid参数，则表示dhcpcd在您的配置中找不到正确的身份验证令牌。

【解读：设置不同的认证协议的key】

### background

Fork to the background immediately.  This is useful for startup scripts which don't disable link messages for carrier status.

【解读：后台异步启动】

### blacklist [address[/cidr]]

Ignores all packets from address[/cidr].

忽视黑名单地址的所有消息

【解读：不被黑名单的DHCPCD服务器修改配置】

### whitelist [address[/cidr]]

Only accept packets from address[/cidr]. blacklist is ignored if whitelist is set.

只接收白名单地址的所有消息

【解读：只被白名单的DHCPCD服务器修改配置】

### bootp

Be a BOOTP client.  Basically, this just doesn't send a DHCP Message Type option and will only interact with a BOOTP server.  All other DHCP options still work.

【解读：作为一个bootp协议客户端】

### broadcast

Instructs the DHCP server to broadcast replies back to the client.  Normally this is only set for non-Ethernet interfaces, such as FireWire and InfiniBand.  In most cases,
dhcpcd will set this automatically.

【解读：指示DHCP服务端通过广播的方式回复DHCP包】

### controlgroup group

Sets the group ownership of /var/run/dhcpcd/sock so that users other than root can connect to dhcpcd

【解读：设置/var/run/dhcpcd/sock文件的用户组权限，该文件用于和DHCPCD进程通信】

### debug

Echo debug messages to the stderr and syslog.

【解读：输出debug消息到标准错误和syslog】

### dev [value]

Load the value /dev management module.  dhcpcd will load the first one found to work, if any.

【ERROR：作用未知】

### env [value]

push value to the environment for use in dhcpcd-run-hooks(8).  For example, you can force the hostname hook to always set the hostname with env force_hostname=YES.  Or set which driver wpa_supplicant(8) should use with `env wpa_supplicant_driver=nl80211`.If the hostname is set, it will be will set to the FQDN if possible as per RFC 4702, section 3.1.  If the FQDN option is missing, dhcpcd will still try and set a FQDN from the hostname and domain options for consistency.  To override this, set env hostname_fqdn=[YES|NO|SERVER].  A value of SERVER means just what the server says, don't manipulate it. This could lead to an inconsistent hostname on a DHCPv4 and DHCPv6 network where the DHCPv4 hostname is short and the DHCPv6 has an FQDN.  DHCPv6 has no hostname option.

【解读：将环境变量值推送到dhcpcd-run-hooks脚本中使用。另外需要注意的是，如果配置hostname的时候需要配置FQDN，因为IPV6使用FQDN，不适用hostname，如果不配置FQDN会导致IPV4和IPV6的网络主机名显示不同。】

### clientid [string]

Send the clientid.  If the string is of the format 01:02:03 then it is encoded as hex.  For interfaces whose hardware address is longer than 8 bytes, or if the clientid is an empty string then dhcpcd sends a default clientid of the hardware family and the hardware address.

发送客户端ID。如果字符串的格式为01:02:03，则将其编码为十六进制。对于硬件地址长于8字节的接口，或者如果客户端ID是空字符串，则dhcpcd发送硬件系列的默认客户端ID和硬件地址。

【解读：每个dhcp客户端有个自己的客户端，默认情况下dhcpcd会自己根据mac生成clientid，如果要手动设置建议长度和mac一样】

### duid [ll | lt | uuid | value]

Use a DHCP Unique Identifier. If a system UUID is available, that will be used to create a DUID-UUID, otherwise if persistent storage is available then a DUID-LLT (link local address + time) is generated, otherwise DUID-LL is generated (link local address).  The DUID type can be hinted as an optional parameter if the file /var/db/dhcpcd/duid does not exist.  If not ll, lt or uuid then value will be converted from 00:11:22:33 format.  This, plus the IAID will be used as the clientid.  The DUID generated will be held in /var/db/dhcpcd/duid and should not be copied to other hosts.  This file also takes precedence over the above rules except for setting a value.

【解读：DUID 是永久性的，通常在设备的整个生命周期内保持不变。DUID 主要用于 DHCPv6，但也可以在 DHCPv4 中使用。它用于确保在 IPv6 网络中唯一标识客户端。】

### iaid [iaid]

Set the Interface Association Identifier to iaid.  This option must be used in an interface block.  This defaults to the VLANID (prefixed with 0xff) for the interface if set, otherwise the last 4 bytes of the hardware address assigned to the interface.  Each instance of this should be unique within the scope of the client and dhcpcd warns if a con‐ flict is detected.  If there is a conflict, it is only a problem if the conflicted IAIDs are used on the same network.

【解读：IAID 通常基于网络接口生成，通常是网络接口的索引或其他唯一信息。每个网络接口都有一个唯一的 IAID，即使是在同一设备上的多个接口。】

### dhcp

Enable DHCP on the interface, on by default.

【解读：开启dhcp】

### dhcp6

Enable DHCPv6 on the interface, on by default.

【解读：开启dhcpv6】

### ipv4

Enable IPv4 on the interface, on by default.

【解读：开启ipv4，默认开】

### ipv6

Enable IPv6 on the interface, on by default.

【解读：开启ipv6，默认开】

### request [address]

Request the in the DHCP DISCOVER message. There is no guarantee this is the address the DHCP server will actually give. If no is given then the first address currently assigned to the is used.addressaddressinterface

【解读：发送`DHCP DISCOVER`请求获取一个特定的IP地址，不过需要注意的是，DHCP服务器会尽量提供，但不保证。】

### inform [address[/cidr[/broadcast_address]]]

Behaves like as above, but sends a DHCP INFORM instead of DISCOVER/REQUEST. This does not get a lease as such, just notifies the DHCP server of the in use. You should also include the optional network number in case the address is not already configured on the interface. remains running and pretends it has an infinite lease. will not de-configure the interface when it exits. If fails to contact a DHCP server then it returns a failure instead of falling back on IPv4LL.requestaddresscidrdhcpcddhcpcddhcpcd

【解读：发送`DHCP INFORM`通知DHCP服务器当前使用的地址，用于获取其他配置项，同时保证IP网络正常】

### inform6

Performs a DHCPv6 Information Request. No address is requested or specified, but all other DHCPv6 options are allowed. This is normally performed automatically when an IPv6 Router Advertisement indicates that the client should perform this operation. This option is only needed when is not processing IPv6 RA messages and the need for a DHCPv6 Information Request exists.dhcpcd

【解读：获取IPV6除IP地址以外的其他信息。另外当 dhcpcd 未处理（或配置了不处理） IPv6 路由通告消息时，管理员可以使用这个配置项手动触发 DHCPv6 信息请求。】

### persistent

dhcpcd normally de-configures the interface and configuration when it exits. Sometimes, this isn't desirable if, for example, you have root mounted over NFS or SSH clients connect to this host and they need to be notified of the host shutting down. You can use this option to stop this from happening.

【解读：persistent 配置项用于防止 dhcpcd 在退出时去配置网络接口和相关配置。这对于某些关键场景（如根文件系统通过 NFS 挂载或需要保持 SSH 连接）非常有用，确保在 dhcpcd 退出时网络连接和配置保持不变。】

### fallback [profile]

Fall back to using this profile if DHCP fails. This allows you to configure a static profile instead of using ZeroConf.

【解读：fallback profile 配置项用于在 DHCP 失败时自动切换到一个预定义的静态配置文件，而不是使用 ZeroConf 自动配置链路本地地址。这可以提高网络的稳定性和可靠性，确保在 DHCP 不可用的情况下，网络接口仍然能够正常工作。】

### hostname [name]

Sends the hostname to the DHCP server so it can be registered in DNS. If is an empty string then the current system hostname is sent. If is a FQDN (i.e., contains a .) then it will be encoded as such.namenamename

【解读：hostname name 配置项的主要作用是将主机名发送给 DHCP 服务器，以便在 DNS 中进行注册。如果 name 是空字符串，则发送当前系统的主机名；如果 name 是 FQDN，则按照 FQDN 格式进行编码并发送。这个配置项有助于确保网络中的设备能够通过主机名进行访问和管理。】

### hostname_short

Sends the short hostname to the DHCP server instead of the FQDN. This is useful because DHCP servers will not register the FQDN in their DNS if the domain part does not match theirs.Also, see the option above to control how the hostname is set on the host.env

【解读：和hostname关联使用，发送域名的时候不发送域名后缀。这在避免域名不匹配问题、确保主机名能够在 DHCP 服务器的 DNS 中注册方面非常有用。】

### ia_na [address] [iaid]

Request a DHCPv6 Normal Address for . defaults to the option as described above. You can request more th

【解读：ia_na 配置项的主要作用是请求一个或多个 DHCPv6 正常地址，并为每个地址请求指定一个唯一的 IAID。通过这种方式，客户端可以通过 DHCPv6 协议获取所需的 IPv6 地址，并确保每个地址请求都有唯一的标识符进行区分。】

### ia_ta [iaid]

Request a DHCPv6 Temporary Address for . You can request more than one ia_ta by specifying a unique for each one.iaidiaid

【解读：请求一个或者多个临时的IPV6地址，不同的临时地址需要不同的iaid。临时地址会定期变更。】

### ia_pd [iaid [/ prefix / prefix_len] [interface [/ sla_id [/ prefix_len [/ suffix]]]]]

Request a DHCPv6 Delegated Prefix for . This option must be used in an block. Unless a of 0 is assigned with the same resultant prefix length as the delegation, a reject route is installed for the Delegated Prefix to stop unallocated addresses being resolved upstream. If no is given then we will assign a prefix to every other interface with a equivalent to the interface index assigned by the OS. Otherwise addresses are only assigned for each and . Each assigned address will have a , defaulting to 1. If the is 0 then a SLAAC address is assigned. You cannot assign a prefix to the requesting interface unless the DHCPv6 server supports the Prefix Exclude Option. has to be running for all the interfaces it is delegating to. A default of 64 is assumed, unless the maximum does not fit. In this case is increased to the highest multiple of 8 that can accommodate the . is an integer which must be unique inside the and is added to the prefix which must fit inside less the length of the delegated prefix. You can specify multiple per , space separated. IPv6RS should be disabled globally when requesting a Prefix Delegation. iaidinterfacesla_idinterfacesla_idinterfacesla_idsuffixsuffixRFC 6603dhcpcdprefix_lensla_idprefix_lensla_idsla_idiaidprefix_leninterface /sla_id /prefix_lenia_pd.In the following example eth0 is the externally facing interface to be configured for both IPv4 and IPv6. The DHCPv4 server will provide us with an IPv4 address and a default route. The DHCPv6 server is going to provide us with an IPv6 address, a default route and a /64 subnet to be delegated to the internal interface. The eth1 interface will be automatically configured for IPv6 using the first address (::1) from the delegated prefix. A second prefix is requested and assigned to two other interfaces. rtadvd(8) can be used with an empty configuration file on eth1, eth2 and eth3, to provide automatic IPv6 address configuration for the internal network.

```
noipv6rs                 # disable routing solicitation
denyinterfaces eth2      # Don't touch eth2 at all
interface eth0
  ipv6rs                 # enable routing solicitation get the
                         # default IPv6 route
  ia_na 1                # request an IPv6 address
  ia_pd 2 eth1/0         # request a PD and assign it to eth1
  ia_pd 3 eth2/1 eth3/2  # req a PD and assign it to eth2 and eth3
```

【解读：ia_pd 配置项的主要作用是请求一个或多个 DHCPv6 前缀委派，并为每个前缀请求指定一个唯一的 IAID。前缀委派允许客户端从 DHCPv6 服务器获取一个 IPv6 前缀，并使用该前缀在内部网络上分配地址。具体如何二次分发需要再深入研究。】

### ipv4only

Only configure IPv4.

【解读：全局，DHCP 客户端仅配置 IPv4 地址】

### ipv6only

Only configure IPv6.

【解读：全局，DHCP 客户端仅配置 IPv6 地址】

### fqdn [disable | none | ptr | both]

none will not ask the DHCP server to update DNS. just asks the DHCP server to update the PTR record of the host in DNS, whereas also updates the A record. will disable the FQDN option. The default is . itself never does any DNS updates. encodes the FQDN hostname as specified in .ptrbothdisablebothdhcpcddhcpcdRFC 1035

【解读：控制dhcpcd怎么更新dns信息，不更新A和PTR记录，更新A不更新PTR，或者....，排列组合】

### interface [interface]

Subsequent options are only parsed for this .interface

【解读：指定后续的配置选项仅适用于特定的网络接口。如interface eth0】

### 