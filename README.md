1. speed dxl
이 코드는 Dynamixel 모터의 속도를 제어하고 실행 시간을 측정하는 프로그램입니다. 주요 흐름을 설명하면 다음과 같습니다:

시그널 핸들러 설정:

signal(SIGINT, ctrlc);를 통해 Ctrl+C 신호를 받으면 ctrlc() 함수가 호출되어 ctrl_c_pressed 변수를 true로 설정합니다. 이를 통해 프로그램을 안전하게 종료할 수 있습니다.
Dynamixel 장치 열기:

Dxl 객체 mx의 open() 함수를 호출해 장치를 엽니다. 만약 열리지 않으면 오류 메시지를 출력하고 프로그램을 종료합니다.
반복 루프 (속도 입력 및 설정):

while 루프가 계속되며 사용자가 왼쪽과 오른쪽 모터 속도를 입력하도록 요청합니다.
mx.setVelocity(leftspeed, rightspeed);를 호출해 입력된 속도를 Dynamixel 모터에 설정합니다.
usleep(20*1000);로 20ms 대기합니다.
gettimeofday로 루프 실행 시간을 측정하고 출력합니다.
ctrl_c_pressed가 true일 경우 루프를 종료합니다.
Dynamixel 장치 닫기:

루프가 끝나면 mx.close();를 호출하여 Dynamixel 장치를 닫고 프로그램을 종료합니다.
++ 1. Ctrl+C를 눌러도 바로 종료되지 않는 이유
Ctrl+C를 눌러도 즉시 종료되지 않고 명령을 한 번 더 입력해야 종료되는 이유는, ctrl_c_pressed 플래그가 true로 설정되었더라도 cin이 입력을 대기하는 동안 반복문이 멈추기 때문입니다. 따라서 Ctrl+C를 누르더라도 cin 대기 상태가 해제되지 않으면 ctrl_c_pressed를 체크하는 코드에 도달하지 않아 종료되지 않습니다.

해결 방법
cin 대기 상태를 해제하고 바로 반복문을 탈출할 수 있도록 하기 위해서는 비차단 입력을 구현하거나 cin 전후에 ctrl_c_pressed를 추가로 확인하는 방법이 있습니다. 예를 들어, 다음과 같이 cin에 진입하기 전과 후에 ctrl_c_pressed를 확인하여 즉시 종료할 수 있습니다.
++2. 급가속 및 급감속 문제 해결 방법
Dynamixel 모터에 속도 명령을 전송할 때, 현재 속도와 목표 속도의 차이가 크면 모터가 급격하게 속도를 변화시키며 진동과 소음을 발생시킬 수 있습니다. 이를 해결하려면 속도 변화량을 제한하여 목표 속도에 천천히 도달하도록 해야 합니다.

해결 방법: 속도 변화량 제한
각 반복에서 목표 속도로의 변화량을 제한하여 서서히 목표 속도로 도달하게 만들 수 있습니다. 예를 들어, 현재 속도에서 일정 값만큼씩 증가 또는 감소하도록 하면 진동과 소음을 줄일 수 있습니다.

2. keyboard dxl
이 프로그램은 키보드 입력을 통해 Dynamixel 모터의 속도를 제어하며, 각 반복에서 실행 시간을 측정해 출력합니다. 주요 흐름을 설명하겠습니다.

코드 주요 흐름
시그널 핸들러 설정:

signal(SIGINT, ctrlc_handler);를 통해 Ctrl+C가 눌리면 ctrlc_handler() 함수가 호출되어 ctrl_c_pressed를 true로 설정합니다. 이로써 프로그램이 안전하게 종료될 수 있습니다.
Dynamixel 장치 열기:

Dxl 객체 mx의 open() 함수를 호출하여 장치를 엽니다. 장치가 열리지 않으면 오류 메시지를 출력하고 프로그램이 종료됩니다.
반복 루프 (속도 설정 및 출력):

키 입력 체크: mx.kbhit()로 키보드 입력을 감지합니다.

키가 입력되면 mx.getch()를 통해 입력된 문자를 확인하고, switch 문을 사용하여 속도 명령을 설정합니다:
's': 정지 (vel1 = 0, vel2 = 0)
'f': 전진 (vel1 = 50, vel2 = -50)
'b': 후진 (vel1 = -50, vel2 = 50)
'l': 좌회전 (vel1 = -50, vel2 = -50)
'r': 우회전 (vel1 = 50, vel2 = 50)
설정된 속도를 mx.setVelocity(vel1, vel2);를 통해 모터에 전달합니다.
종료 조건: ctrl_c_pressed가 true이면 반복문을 종료합니다.

지연 시간 및 실행 시간 측정:

usleep(20*1000);으로 20ms 대기합니다.
gettimeofday를 사용해 루프 실행 시간을 time1에 계산하고 출력합니다.
Dynamixel 장치 닫기:

루프가 종료되면 mx.close();를 호출하여 장치를 닫고 프로그램을 종료합니다.
출력 예시
반복문이 진행될 때마다 현재 속도(vel1, vel2)와 실행 시간(time1)이 출력됩니다.
