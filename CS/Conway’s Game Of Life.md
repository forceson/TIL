# Conway’s Game Of Life
최근 풀어본 알고리즘 문제 중에 Conway’s Game Of Life라는 문제가 있었다.

규칙은 다음과 같다.

1. 격자무늬 바탕에 각 칸마다 '세포'가 채워져있다.

2. 세포들은 각각 '산 상태' 또는 '죽은 상태'를 갖는다.

3. 각 세포 주변의 8개의 세포(상하좌우,대각선4방향)을 '이웃'이라 한다.

4. 죽어있는 세포의 이웃중 3명만이 살아있다면, 이 세포는 살아난다.

5. 살아있는 세포의 이웃중 2명 또는 3명만이 살아있다면, 이 세포는 여전히 살고, 그 외엔 죽는다.

6. 세포의 생존 여부는 이번 세대에서 결정되어 다음 세대에서 나타난다.

해당 문제를 풀고 실행해보면 지속적으로 반복되는 패턴들이 등장하기도 한다.
```java
    int[][] grid = {
                    {0, 0, 0, 0, 0, 0, 0, 0, 0, 0},
                    {0, 0, 0, 1, 1, 1, 0, 0, 0, 0},
                    {0, 0, 0, 0, 0, 0, 0, 0, 0, 0},
                    {0, 0, 0, 0, 0, 0, 0, 0, 0, 0},
                    {0, 0, 0, 0, 0, 0, 0, 0, 0, 0},
                    {0, 0, 0, 1, 1, 0, 0, 0, 0, 0},
                    {0, 0, 1, 1, 0, 0, 0, 0, 0, 0},
                    {0, 0, 0, 0, 0, 1, 0, 0, 0, 0},
                    {0, 0, 0, 0, 1, 0, 0, 0, 0, 0},
                    {0, 0, 0, 0, 0, 0, 0, 0, 0, 0}
            };
    while (true) {
        int[][] newGrid = new int[10][10];
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid.length; j++) {
                int count = 0;
                for (int m = i - 1; m <= i + 1; m++) {
                    for (int n = j - 1; n <= j + 1; n++) {
                        if (m < 0 || m >= grid.length || n < 0 || n >= grid.length) continue;
                        if (m == i && n == j) continue;
                        if (grid[m][n] == 1) {
                            count++;
                        }
                    }
                }
                if (grid[i][j] == 1 && (count == 2 || count == 3)) newGrid[i][j] = 1;
                else if (grid[i][j] == 0 && count == 3) newGrid[i][j] = 1;
            }
        }
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid.length; j++) {
                grid[i][j] = newGrid[i][j];
                System.out.print(newGrid[i][j] + " ");
            }
            System.out.println();
        }
        final String operatingSystem = System.getProperty("os.name");
        try {
            if (operatingSystem.contains("Windows")) {
                Runtime.getRuntime().exec("cls");
            } else {
                Runtime.getRuntime().exec("clear");
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.print("\033[H\033[2J");
        System.out.flush();
    }
```
어쩌면 복잡성은 여기서 상관없었을지도 모르겠다. 구현이 중요했을 수도.
