SSTF
===========================
Building Trust through Security
------------------------------------------------

<table>
    <thead>
        <tr>
            <th colspan='2'>Technology Perspective </th> 
            <th colspan='2'> Society Perspective </th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th> 5G </th>
            <th> AI </th>
            <th> BIg Data </th>
            <th> IoT </th> 
        </tr>
        <tr></tr>
        <tr>
            <th>Trust </th>
            <th> Protection (Knox) </th>
            <th> Detection </th>
            <th> Prevention </th>
        </tr>
    </tbody>
</table>

Building Trustworthy Software Foundation with Hardware Security Mechanisms
------------------------------------------------------

1. Moore's law -> 삼보에서 2년 뒤 업그레이드 제공
2. Extensions
    1. Hardware -> Software
    2. 최근 Extension은 Security에 영향을 미침
    3. Meltdown, Spectre (Speculative Execution / Out-of-order Execution)
    4. Hyper-Threads, Store Buffer, etc -> (MDS-class atatcks)
    5. Last Level Cache, prefetch, TSX, etc -> (위의 3개는 Performance를 위해 디자인된 Extension)
3. TSX
    1. ```if(xbegin() == XBEGIN_STARTED) {*kptr; //exception -> abort xend();}```
    2. 방어로 할용 : 예상되지 않은 Abort가 발생할 경우 공격이 발생했다고 판단
4. Intel PT
    1. Made for Debugging
    2. Can Make Control Flow Graph (Sort of Flow chart?)
        1. Full trace -> When program is really executed
        2. There is no need to trace all nodes -> PT trace
    3. PT -> Increase Precison of CFG
        1. CFG? -> When compiled?? The node can be linked? 
            1. if node can't be linked is linked : Decide the execution is attack!
        2. More Precison == Comsume More time when compile
        3. PT work asyncronosly
            1. We can know what code is vulnarable
            2. So we only trace the code can be attacked(e.g. read, write)
5. Intel Memory Protection
    1. Can Change Memory's attribute