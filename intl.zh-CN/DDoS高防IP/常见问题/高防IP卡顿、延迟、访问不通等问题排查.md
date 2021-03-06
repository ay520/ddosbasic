# 高防IP卡顿、延迟、访问不通等问题排查 {#concept_55870_zh .concept}

## 问题描述 {#section_xjy_sqk_ggb .section}

客户端访问高防IP异常卡顿，出现较大延迟、丢包现象。

## 分析思路 { .section}

遇到此类问题时，建议您收集受影响的客户端源IP，并通过 Traceroute 信息或 MTR 命令等工具进行链路测试，来判断问题来源。

## 排查方法 { .section}

-   **业务本身是否存在跨网访问**

    高防IP服务支持电信、联通、及BGP三种线路。其中，BGP线路用于优化移动及小运营商网络质量。

    -   当非电信（如联通，移动等）用户端跨网访问电信线路时，存在一定延迟和丢包。
    -   当非联通（如电信，移动等）用户端跨网访问联通线路时，存在一定延迟和丢包。
    优化建议：电信用户端通过电信线路访问，联通用户端通过联通线路访问，移动及其他线路通过BGP线路访问。

-   **后端服务器是否有异常**

    根据出现异常的高防IP配置的源站类型进行排查。

    -   **源站是负载均衡（SLB）**

        参照以下步骤进行排查。

        1.  针对负载均衡IP和端口，通过运行 tcping 工具，查看记录是否有异常。
        2.  查看负载均衡服务器状态（如连接数情况、后端服务器）是否有异常状态。
        3.  查看负载均衡是否设置黑、白名单，或者其他的访问控制策略，确认放行高防本身回源IP段。
        4.  查看负载均衡后端ECS、VPC，确认是否有安全软件或其它IP封禁策略。

            **说明：** 配置负载均衡\(SLB\)后，后端服务器无法看到访问真实源IP。如果有安全软件进行恶意IP识别并阻断，一般情况都是高防集群本身的回源IP，而此类IP都需要放行。

        5.  查看负载均衡IP是否暴露。
    -   **源站是云服务器（ECS/VPC）**

        参照以下步骤进行排查。

        1.  针对服务器IP和端口，通过运行 tcping 工具查看记录是否有异常。
        2.  查看后端服务器是否有异常事件，如服务器本身黑洞及清洗事件、CPU高、数据库请求慢、出方向带宽满等。
        3.  查看服务器本身是否设置黑、白名单，或者其他的访问控制策略，确认放行高防本身回源IP段。
        4.  查看ECS服务器或VPC，确认是否有安全软件或其它IP封禁策略阻断高防回源IP。
        5.  查看服务器IP是否暴露。
    -   **源站是非阿里云服务器**

        参照以下步骤进行排查。

        1.  针对服务器IP和端口，通过运行 tcping 工具查看记录是否有异常。
        2.  查看服务器是否有异常事件，如CPU高、数据库请求慢、出方向带宽满等。
        3.  查看服务器本身是否设置黑、白名单，或者其他的访问控制策略，确认放行高防本身回源IP段。
        4.  查看服务器，确认是否有安全软件或其它IP封禁策略阻断高防回源IP。

            **说明：** 非阿里云服务器一般都无法识别访问真实源IP，如果有安全软件进行恶意IP识别并阻断，一般情况都是高防集群本身的回源IP，而此类IP都需要放行。

        5.  查看服务器IP是否暴露。

            **说明：** 配置高防IP服务后，建议您更换后端源站服务器IP，不要使用之前已暴露的IP。

        如果您使用的是阿里云产品，发现异常并需售后技术支持团队协助进行排查，请提交相关云产品工单，并说明情况。

-   **高防IP是否有清洗事件**
    -   **高防IP有清洗事件**

        参照以下步骤进行排查。

        1.  针对受攻击端口，通过运行 tcping 工具查看是否有延迟和丢包，并记录。
        2.  针对未被攻击端口，通过运行 tcping 工具查看是否有延迟和丢包，并记录。
        根据记录结果，对照下表查看问题原因。

        |受攻击端口是否有延时、丢包|未被攻击端口是否有延时、丢包|问题原因分析|
        |-------------|--------------|------|
        |是|否|说明清洗策略未误杀，查看后端服务器状态是否异常，确认后端服务器抗攻击性能。若服务器抗攻击能力较弱，则需要收紧防御策略。|
        |是|是|清洗策略误杀导致。请提交工单，需要进行后端排查。|
        |否|否|非清洗策略原因。|
        |否|是|一般不存在这种情况。|

        上述前两种情况，建议您通过工单说明情况，并提交售后技术支持团队来协助您处理。若需要收紧防御策略，您需要提供服务器抗攻击能力的详细参数，包括：

        -   正常用户访问情况
        -   业务主要交互过程
        -   应用对外服务能力
    -   **高防IP没有清洗事件**

        说明问题非攻击导致。

-   **高防IP有黑洞事件**

    在有黑洞事件时，请确认进入黑洞的IP，以及受影响访问是否都经过该IP。在一定条件下，高防IP被黑洞后具备自动切换功能，但客户端实际生效时间依赖于DNS解析、本地DNS缓存和更新时间。

    -   **网站接入**

        在**高防IP** \> **网站**页面，确认是否开启CNAME自动调度功能。CNAME自动调度可以确保当某个线路的高防IP出现问题时，自动把业务切换到其他正常的线路，提供灾备能力，保证业务的连续性和可用性。

        -   当电信线路的IP被黑洞时，可以自动摘除电信IP的解析，只解析到联通和BGP线路。
        -   当联通线路的IP被黑洞时，可以自动摘除联通IP的解析，只解析到电信和BGP线路。
        -   当BGP线路的IP被黑洞时，可以自动摘除BGP IP的解析，只解析到电信和联通线路。
    -   **非网站接入**

        非网站接入方式没有CNAME切换调度逻辑，请确认是否自身进行调度。

        建议：对应用进行调整，使其具备切换能力。当线路被黑洞时，可切换至正常线路。

-   **其他**

    若问题仍未解决，请提交工单联系售后技术支持团队。为便技术支持团队快速判断及分析问题，请在工单内提供以下访问情况信息。

    |线路|影响访问源IP|被访问高防IP|Ping信息|traceroute或mtr信息|tcping或端口连接信息|
    |--|-------|-------|------|----------------|-------------|
    |电信线路|例如：1.1.1.1|例如：180.97.163.0|提供连续且大于10次 ping 请求的结果。|提供从此访问源IP至被访问高防IP的 tracert 或 traceroute 信息。|提供连续且大于10次的 tcpping 或端口连接信息。|
    |联通线路|...|...|...|...| |
    |BGP线路|...|...|...|...| |

    您也可以在工单中补充以下信息，方便售后技术支持团队快速定位问题：

    -   高防IP配置回源IP的类型，如负载均衡、云主机（ECS/VPC）、非阿里云服务器。
    -   回源IP，及负载均衡、云主机（ECS/VPC）、或非云阿里云服务器的日志，如CPU、内存、带宽、连接数等数据。
    -   源站是否存在访问控制策略。
    -   源站是否有安装安全软件，如云锁、360、安全狗、自带iptables等。
    -   源站是否有安全策略，如针对IP级别的检测及过滤。
    -   高防IP是否有清洗及黑洞事件。
    -   业务类型，如网站、端游、页游、APP等。
    -   问题出现时间，是否有其他涉及更改、删除高防IP实例的操作。
     


## 常用检查工具的使用及介绍 { .section}

**Traceroute 命令行工具**

Traceroute 是 Linux 预装的网络测试工具，用于跟踪 Internet 协议（IP）数据包传送到目标地址时经过的路径。

Traceroute 会发送具有最大存活时间值（Max\_TTL）的 UDP 探测数据包，然后侦听从网关开始的整个链路上的 ICMP TIME\_EXCEEDED 响应。探测从 TTL=1 开始，TTL 值逐步增加，直至接收到 ICMP PORT\_UNREACHABLE 消息。ICMP PORT\_UNREACHABLE 消息用于标识目标主机已经被定位，或命令已经达到允许跟踪的最大 TTL 值。

**说明：** Traceroute 默认发送 UDP 数据包进行链路探测。您可以使用 -I 参数来指定发送 ICMP 数据包用于探测。

示例

```
[root@centos ~]# traceroute -I 223.5.5.5
traceroute to 223.5.5.5 (223.5.5.5), 30 hops max, 60 byte packets
 1  * * *
 2  192.168.17.20 (192.168.17.20)  3.965 ms  4.252 ms  4.531 ms
 3  111.1.20.41 (111.1.20.41)  6.109 ms  6.574 ms  6.996 ms
 4  111.1.34.197 (111.1.34.197)  2.407 ms  2.451 ms  2.533 ms
 5  211.138.114.25 (211.138.114.25)  1.321 ms  1.285 ms  1.304 ms
 6  211.138.114.70 (211.138.114.70)  2.417 ms 211.138.114.66 (211.138.114.66)  1.857 ms 211.138.114.70 (211.138.114.70)  2.002 ms
 7  42.120.244.194 (42.120.244.194)  2.570 ms  2.536 ms 42.120.244.186 (42.120.244.186)  1.585 ms
 8  42.120.244.246 (42.120.244.246)  2.706 ms  2.666 ms  2.437 ms
 9  * * *
10  public1.alidns.com (223.5.5.5)  2.817 ms  2.676 ms  2.401 ms

```

**TRACERT 命令行工具**

TRACERT \(Trace Route\) 是 Windows 自带的网络诊断命令行实用程序，用于跟踪 Internet 协议 \(IP\) 数据包传送到目标地址时经过的路径。

TRACERT 通过向目标地址发送 ICMP 数据包来确定到目标地址的路由。在这些数据包中，TRACERT 使用不同的 IP “生存期” \(TTL\) 值。由于要求沿途的路由器在转发数据包前至少必须将 TTL 减少 1，因此 TTL 实际上相当于一个跃点计数器 \(hop counter\)。当某个数据包的 TTL 达到零 \(0\) 时，相应节点就会向源计算机发送一个 ICMP “超时” 消息。

TRACERT 第一次发送 TTL 为 1 的数据包，并在每次后续传输时将 TTL 增加 1，直到目标地址响应或达到 TTL 的最大值。中间路由器发送回来的 ICMP “超时” 消息中包含了相应节点的信息。

样例

```
C:\> tracert -d 223.5.5.5
通过最多 30 个跃点跟踪到 223.5.5.5 的路由

  1     *        *        *     请求超时。
  2     9 ms     3 ms    12 ms  192.168.17.20
  3     4 ms     9 ms     2 ms  111.1.20.41
  4     9 ms     2 ms     1 ms  111.1.34.197
  5    11 ms     *        *     211.140.0.57
  6     3 ms     2 ms     2 ms  211.138.114.62
  7     2 ms     2 ms     1 ms  42.120.244.190
  8    32 ms     4 ms     3 ms  42.120.244.238
  9     *        *        *     请求超时。
 10     3 ms     2 ms     2 ms  223.5.5.5

跟踪完成。

```

更多关于 TRACERT 命令行工具的信息，请查看[链路测试工具使用及介绍](https://help.aliyun.com/knowledge_detail/40573.html)。

**TCPing工具**

TCPing工具使用TCP的方式去查看端口情况，可以检测出TCP延迟及连接情况。[下载TCPing工具](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/55870/cn_zh/1498570697864/Tcping.zip)。

-   **Windows使用方法**

    将TCPing工具拷贝至 Windows 指定目录，在命令提示行中运行`tcping www.aliyun.com 80`。

    样例

    ```
    C:\>tcping.exe www.aliyun.com 80
    
    Probing 140.205.62.8:80/tcp - Port is open - time=19.550ms
    Probing 140.205.62.8:80/tcp - Port is open - time=8.761ms
    Probing 140.205.62.8:80/tcp - Port is open - time=10.899ms
    Probing 140.205.62.8:80/tcp - Port is open - time=13.013ms
    
    Ping statistics for 140.205.62.8:80
         4 probes sent.
         4 successful, 0 failed.
    Approximate trip times in milli-seconds:
         Minimum = 8.761ms, Maximum = 19.550ms, Average = 13.056ms
    
    ```

-   **Linux使用方法**

    运行以下命令，安装tcping工具。

    ```
    tar zxvf tcping-1.3.5.tar.gz
    cd tcping-1.3.5
    make tcping.linux
    
    ```

    参照以下样例，来使用tcping工具。

    ```
    [root@aliyun tcping-1.3.5]# for ((i=0; i<10; ++i)) ; do ./tcping  www.aliyun.com 80;done
    www.aliyun.com port 80 open.
    www.aliyun.com port 80 open.
    www.aliyun.com port 80 open.
    www.aliyun.com port 80 open.
    www.aliyun.com port 80 open.
    www.aliyun.com port 80 open.
    www.aliyun.com port 80 open.
    www.aliyun.com port 80 open.
    www.aliyun.com port 80 open.
    www.aliyun.com port 80 open.
    
    ```


