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
    1. ```C
        if(xbegin() == XBEGIN_STARTED) {
            *kptr; //exception -> abort
            xend();
            }
        ```
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
    1. Can Change Whole Memory's attribute (at once!!!!)
    2. JIT compile
        1. Dependent on mprotect function
        2. ```javascript
             function init()
                pkey = pkey_alloc()
                pkey_mprotect(code_cache, len, RWX, pkey)
            ```
        3. number of pkey <= 16
        4. Another CPU's pkey is not updated
        5. Key Virtualization
            1. Physical Key -> Software Key
            2. libmpk
                1. OS get CPU's memory give up
                    1. change Memory's attribute
        6. JIT protection
            1. libmpk
                1. Enhanced Performance
                2. Enhanced Security
6. Root of Trust based on Hardware
    1. SGX(Software Guard Extensions)
        1. x86 ISA
        2. Native performance
        3. Already Using Generally
        ***
        4. Isolated execution
            1. Previlege Software Protection
            2. CPU has memory encryption engine
                1. every data passed this is encrypted
                2. Can't be accessed by another things
        5. Remote attestation
            1. is Realy running with SGX?(is really Protected?)
            2. Tor
                1. Protect from traffic analysis
                2. Onion Routing
                    1. Decide what Tor relay to use
                    2. Tor relays can't understand what the packet is(because it's onion-ed)
                3. Problems
                    1. If we select trustable relay it can assure it is protected
                    2. but tor relay can't be all trusted
                    3. Some Agencys are having effecton massive amount of Tor network
                        1. Tor project analysis what relay is attacked and remove the node
                4. Solving
                    1. Use SGX
                        1. Tagging attack
                            ***
                            Even if I selected trustable middle relay but end can know my information
                        2. Bandwidth attack
                5. Pros
                    1. Only one trusted node can ensure protected packet
                    2. Seals information with SGX
7. CPU
    1. CPU vendors are compete with how secured their processor is
    2. Even if the feature is not designed for security can used for security(e.g. CFG)
8. Question
    1. flexable protection
    2. CFG
        1. especially for remote(-> dependent on environment)
            1. trace only when return
    3. Pkey is preceed on page attribute
    4. CFG
        1. runtime variance can be traced?
        2. Answer: generally can't be traced

Reassembler
----------------------------------------
1. Ordinary Reassembler
    1. only for static binding binary
    2. if aslr is adapted it can't symbolize binary
    3. it works with heuristic way so it could symbolize function incorrect
2. Reassmbler
    1.Symbolize
        1. Abbreviation
            1. GOT_addr : base addr
            2. GOT_pointer : &GOT_addr
            3. GOT_offset : @GOT_OFF
        3. Symbolize memory
            1. GOT_pointer trace
                1. trace GOT_pointer
                    1. using stack
                2. calculate where GOT_pointer points to
                3. symbolize
        4. Symbolize jump table
            1. indirect jump
                1. get Use-Define Chain
                2. Symbolize jump table entry
        5. Sybolize lea instructions
            1. find label
            2. Symbolize
    2. Data Memory Layout Preservationi
        1. Seperate memory
3. Test
    1. Coreutils-8.30
        1. It works regardless PIE
        2. overhead
            1. non-PIE
                < 0.2%
            2. PIE
                1.3% ~ 1.5%
4. BiO-ASan
    1. Insert Red-Zone(before/after heap)
    2. Shadow Memory
        1. 8byte alligned
        2. stack is 4byte alligned
        3. Desgined to process these variance
        4. check Red-Zone is changed
    3. Use Case
        1. Overhead
            1. BiO-ASan
                350% ~ 450%
            2. ASan
                800% ~ 11,500%
            3. BiO-Asan is more efficient
3. Conclusion
    1. Found hint
    2. Contribution
        1. Sybolizationi technique
        2. Use Case : BiO-ASan
4. Future Work
    1. Work with Binary analysis tool(B2R2)

Best Practice and Lessons Learned from Security Consulting
------------------------------------------------
1. Theori
    1. Location
        1. Austin / Seoul
    2. Customers
        1. Golbal Corporate
        2. Cryptocurrency
        3. Financial
        4. Game Publisher
        5. Blockchain
        6. Operatin System / Browser
        7. Electronics Manufacturer
    3. Offensive Security
        1. Proactive
        2. Adversarial
            1. 공격자 관점에서 바라보기
        3. Creative
            1. 타겟에 따른 맞춤형 공격
            2. 새로운 공격기법
        4. Assistive
            1. 공격은 최선의 방어
            2. += Risk Assessment, Risk Management
        5. Pros
            1. 공격자 관점에서 검증가능
            2. 최신 공격 기법
        6. Cons
            1. 대응으로 연결되야함 -> 추가적인 노력 필요
    4. Threat model
        1. 누가 공격을 하는가
            1. 개인
            2. 단체
            3. 국가
            4. etc
            5. 불특정 다수 (e.g. WannaCry, GandCrab)
        2. 무엇을 얻으려 하는가
            1. 금전
            2. 명예
            3. 민감한 자료
            ***
                -> 가장 중요한 자산은?
        3. 무엇이 노출되어 있는가
            1. 약점이 무엇인가
            2. 왜 존재하는가
        4. 내가 공격자라면 어떻게 침투할 것인가
            1. 잘 방어 되어있는가
            2. 저렴한 방법
    4. Risk Assessment + Risk Management
        1. Risk Assessment
            1. 위협에 대한 수치화
                1. 공격 가능성
                2. 피해량
            2. Risk Management
                1. 어떻게 관리해야할까
                    1. 현실적인 위협모델 작성
                    2. 위협경로별 관리방법 업데이트
                2. 적절한 투자
                3. 보안 제붐 및 서비스 아니어도 보안성 향상 가능
                    1. 오히려 보안 제품 및 서비스를 적용하여 보인 취약점이 생기는 경우도 있음
2. Case Study
    1. Web Service
        1. 클라이언트 / 서버 취약점
        2. 자주 발견되는 취약점
            1. IDOR(사용자 인증 미흡)
            2. 관리자 / DevOps 노출
            3. Parsing
        3. 위협받는것
            1. 고객 데이터
            2. 회사자산
            3. 사내 보안
        4. 예제 1
            1. 관리자 권한 외부 노출(SSO 로그인 Host 검증 미흡으로 인한 관리자 권한 획득)
            2. 도메인 인증이 없음
        5. 예제 2
            1. .html(innerHTML) 렌더링으로 인한 XSS발생
                1. Vue.js
                    ```javascript
                    <p>
                        Your notes:
                        <span v-html="notes"></span>
                    </p>
                    ```
            2. 어떠한 값이 렌더링 되는지 검증해야함
        6. 예제 3
            1. whereRaw SQl  Injection
                1. Node.js
                    ```javascript
                    const result = awit knex
                        .select(knex.raw(selectClause))
                        .select(selectColumns)
                        .rom(talbe)
                        .where(whereClause)
                        .whereRaw(`DATE(UPLOADED_TIME) >= '${fromTime}'`)
                        .whereRaw(`DATE(UPLOADED_TIME) <= '${toTime}'`)
                    //....
                    ```
            2. 권한 분리 및 필터링
    2. Embedded System
        1. 주요 공격 기법
            1. 소프트웨어 취약점
            2. 하드웨어 디버깅
        2. 자주 발견되는 추약점
            1. Deprecated OS, Software
            2. 디버깅 핀 노출
            3. 원격 접근 벡터
                1. Wi-Fi
                2. Bluetooth
                3. 원격 업데이트
                4. etc
        3. 위협받는 것
            1. 탈옥
            2. 개인정보 유출
        4. 예제 1
            1. 자동차 telemetics 핀 노출
        5. 예제 2
            1. 오래된 소프트웨어
                1. Stagefright + DirtyCOW Full-chai출
            2. 최신 보안 업데이트, 유지보수
        6. 예제 3
            1. Remote Code Execution via Bluetooth
                1. BlueBorne
                2. Braodcom Stack
                    1. Zero-day 취약점 발견
        7. 예제 7
            1. Home Routers RCE via Command Injection
                1. C (MIPS decompiled)
                    ```C
                    {
                        //...
                        // acStack1044 contains user-controlled string
                        sVar2 = strlen(acStack1044);
                        snprintf(acStack1044 + sVar2, 0x400 - sVar2, " &");
                        util_execSystem("oal_startping", acStack1044);
                        return 0;
                    }
                    ```
        8. 배운 점
            1. 지속적인 업데이트
            2. SSDLC 적용
            3. 양산 시 테스팅 / 디버깅 비활성화
            4. OTA 업데이트
                1. 없으면 리콜해야함
    3. 게임 서비스
        1. 주요 공격 기법
            1. 메모리 분석 및 변조
            2. 바이너리 난독화 / 패커 분석
        2. 자주 발견되는 취약점
            1. 불법 핵
            2. 런쳐보인
        3. 위협받는 것
            1. 게임 경제
            2. 인기
        4. 예제 1
            1. 핵, 치팅 프로그램
                1. 월핵, ESP, 에임 핵
                2. 난독화되어 있음
                3. 유저모드 + 커널모드
                4. 유효한 인증서로 서명
        5. 예제 2
            1. P2P 기반 게임 치팅 및 DoS
                1. 각 클라이언트의 데이터를 신뢰
                2. 악성 패킷 전달
        6. 예제 3
            1. 게임런쳐 취약점을 통한 RCE
        7. 배운점
            1. 클라이언트 보안
    4. 금용권
        1. 주요공격기법
            1. 계정 탈취
            2. 제로데이 APT 공격
            3. 관리자 권한 취득
        2. 예제 1
            1. 접근통제 솔루션을 통한 내부망 침투
            2. 보안 솔루션이 오히려 취약점을 만듦
        3. 예제 2
            1. 내부 git repo 엑세스
                1. 접근제어 / 권한분리 미흡
            2. 2FA 활용, 권한 분리
        4. 예제 4
            1. 앱 루팅 탐지 우회
            2. 지금까지 모두 우회가능했음
            3. 보안솔루션이 적용되도 사라지는 것은 아님
        5. 예제 5
            1. 전화번호 조회로 개인정보 유줄
        6. 예제 6
            1. 제로데이 공격
        7. 배운점
            1. 최신 공격기술에 대한 대비
            2. 상세한 로깅
3. 결론
    1. 서비스 특징별 취약점의 영향력이 다름
    2. Offensive Security Research
    3. Active Threat Modelling
    4. Continuous Risk Assessment
    5. Risk Management
    6. Invest in Security
    7. Babystep(하나씩 해나감)
    8. Share Information
Reasearches and Activities for Offensive Security in Samsung Research
----------------------------------------------
Smartphone / Wearable / Smart Devices / 5G / IoT
1. Security
    1. Knox
2. | Defensive Security | Offensive Security |
    |---:|---:|
3. Offensive Security at Samsung Research?
4. Unexpected Attack
    1. 0-day vulnerability
    2. 아기돼지삼형제의 집
        1. 셋째는 늑대에 대비를 함
            -> 공격자에 대비를 해야함
5. 