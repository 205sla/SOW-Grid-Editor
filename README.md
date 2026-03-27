# SOW 그리드 에디터 (SOW Grid Editor)

타워 디펜스 게임 맵을 생성하고 관리하기 위한 단일 파일 HTML 그리드 에디터입니다. `.ini` 형식의 맵 파일과 함께 작동하도록 설계되었습니다.

## 주요 기능

- **시각적 그리드 에디터**: 실시간 미리보기와 함께 드래그 앤 페인트(Drag-and-paint) 방식으로 타일을 배치할 수 있는 인터페이스
- **타일 유형**: PATH, SPAWN, RUNE, BLOCKED 및 BLOCKED 변형(L1-L5) 지원 (선택적 모디파이어 포함)
- **자동 경로 생성**: 길찾기 알고리즘을 사용하여 SPAWN 타일에서 RUNE 타일로 향하는 적의 경로를 자동으로 생성
- **이중 경로 시스템**: 일반 경로와 BLOCKED 지형을 통과할 수 있는 모디파이어 경로(`_M` 접미사)를 모두 생성
- **맵 관리자**: 브라우저의 localStorage를 사용하여 여러 개의 맵(최대 50개) 저장 및 불러오기 기능
- **파일 입출력 (File I/O)**:
  - 맵을 `.ini` 형식으로 내보내기 (원본 게임 포맷과 호환)
  - `.ini` 파일을 가져와 컬렉션에 맵 추가
  - 모든 맵을 `.ini` 또는 `.json` 파일로 일괄 내보내기
  - 여러 개의 `.ini` 파일을 한 번에 가져오기
- **설정 유지**: ClassMap 및 Meta 설정이 브라우저 쿠키(Cookies)에 저장됨
- **한국어 현지화**: 기술 용어는 영어로 유지하면서 전체 UI를 한국어로 제공
- **경로 시각화**: 생성된 경로를 색상으로 구분하여 오버레이 형태로 표시

## 빠른 시작

1. 웹 브라우저에서 `grid-editor.html` 파일을 엽니다.
2. 새로운 맵을 생성하거나 기존 `.ini` 파일을 불러옵니다(Import).
3. 타일 팔레트를 사용하여 그리드에 색칠하듯 타일을 배치합니다.
4. 그리드 크기(가로 × 세로)를 설정합니다.
5. 경로를 자동으로 생성하거나 수동으로 만듭니다.
6. 맵을 브라우저 저장소에 저장하거나 `.ini`/`.json` 파일로 내보냅니다(Export).

## UI 레이아웃

### 상단 툴바
- **Map 버튼**: 맵 관리자 모달창 열기
- **Map Name**: 맵 이름 수정 (기본값: "Untitled")
- **Save 버튼**: 현재 맵을 localStorage에 저장
- **Grid Controls**: 셀 크기, 가로, 세로 입력 및 Apply(적용) 버튼
- **Reset**: 현재 그리드 초기화
- **Import `.ini`**: 단일 파일 불러오기
- **Export `.ini`**: 현재 맵을 `.ini` 파일로 다운로드

### 타일 팔레트
그리드에 칠할 활성 타일 유형을 선택하는 동적 버튼 세트입니다. 다음을 포함합니다:
- PATH (이동 가능한 경로)
- SPAWN (적 스폰 지점)
- RUNE (목표/도착지)
- BLOCKED (기본 지형)
- BLOCKED_L1–L5 (선택적 `_M` 모디파이어가 있는 지형 변형)

### 맵 관리자 모달
- **Map List**: 저장된 모든 맵과 타임스탬프 확인
- **Actions**: 개별 맵 불러오기(Load), 삭제(Delete) 또는 이름 변경(Rename)
- **일괄 작업 (Bulk Operations)**:
  - 모든 맵을 `.ini` 파일로 내보내기 (순차적 다운로드)
  - 모든 맵을 하나의 `.json` 파일로 내보내기
  - 여러 개의 `.ini` 파일을 한 번에 불러오기
- **Reset**: 확인 알림 후 모든 맵 삭제

## 파일 형식

### `.ini` 포맷 구조

```ini
[Meta]
GridWidth=19
GridHeight=10
TileWidth=300
TileHeight=300
Default=BLOCKED

[ClassMap]
TURRET1=/Game/01Blueprints/Tile/Tiles/BP_Tile_Turret_Forest_Stone1...
TURRET2=/Game/01Blueprints/Tile/Tiles/BP_Tile_Turret_Forest_Stone1...
PATH=/Game/01Blueprints/Tile/Tiles/BP_Tile_Path_Default...
BLOCKED=/Game/01Blueprints/Tile/Tiles/BP_Tile_Blocked_Default...
SPAWN1=/Game/01Blueprints/Tile/Tiles/BP_Tile_EnemySpawner...
SPAWN2=/Game/01Blueprints/Tile/Tiles/BP_Tile_EnemySpawner...
RUNE=/Game/01Blueprints/Tile/Tiles/BP_Tile_CoreRune...

[Grid]
Row0=BLOCKED,BLOCKED,BLOCKED,...
Row1=BLOCKED,PATH,PATH,...
...

[EnemyRoutes]
Route1=(2,9)->(2,8)->(2,7)->...
Route1_M=(2,9)->(2,8)->...
Route2=(6,6)->(5,6)->...
```

## 타일 유형

| 유형 | 설명 |
|------|-------------|
| **PATH** | 이동 가능한 지형 (흰색) |
| **SPAWN** | 적 스폰 지점 (빨간색, 번호 부여됨) |
| **RUNE** | 목표/도착지 타일 (파란색) |
| **BLOCKED** | 이동 불가 지형 (어두운 회색) |
| **BLOCKED_L1–L5** | 깊이(Depth) 레벨이 있는 지형 변형 (box-shadow로 시각적 구분) |
| **_M 접미사** | 모디파이어 변형 (모디파이어 경로에서만 이동 가능) |

## 경로 시스템

### 일반 경로
- SPAWN 타일과 RUNE 타일을 연결합니다.
- PATH 타일을 따라 이동합니다.
- `Route1`, `Route2` 등으로 명명됩니다.
- 굵은 선, 불투명도 100%, 실선 스타일로 표시됩니다.

### 모디파이어 경로 (`_M`)
- 일반 경로와 출발/도착지는 같지만 `BLOCKED_L*_M` 타일도 통과할 수 있습니다.
- `Route1_M`, `Route2_M` 등으로 명명됩니다.
- 얇은 선, 불투명도 50%, 점선 스타일로 표시됩니다.
- 짝을 이루는 일반 경로와 동일한 색상을 갖습니다 (예: Route1과 Route1_M은 둘 다 파란색).

경로는 BFS(너비 우선 탐색) 길찾기 알고리즘을 사용하여 자동으로 생성됩니다. 좌표 입력을 통한 수동 편집도 가능합니다.

## 브라우저 저장소

### localStorage
- **Key**: `sow_maps`
- **데이터**: 맵 객체의 JSON 배열 (맵당 약 5KB)
- **용량**: 50개의 맵 ≈ 300KB (브라우저 제한 한도 내에서 충분함)

### Cookies
- **sow_classmap**: ClassMap 구성 (게임 에셋 경로 저장)
- **sow_meta**: Meta 설정 (그리드 크기, 타일 크기 저장)

## 팁

- **BLOCKED_L\*_M** 타일을 사용하여 지형을 관통하는 대체 경로를 만드세요.
- 자동 경로 생성 기능은 타일의 이동 가능 여부를 고려합니다. 커스텀 경로가 필요하다면 수동으로 경로를 편집하세요.
- 브라우저의 일괄 다운로드 차단을 방지하기 위해, 전체 내보내기 시 각 파일 사이에 150ms의 간격을 둡니다.
- 맵 이름은 모든 문자를 지원하며, 공백 및 특수 문자도 그대로 보존됩니다.
- SPAWN 타일은 자동으로 번호가 매겨지며(SPAWN1, SPAWN2 등), 삭제 시 번호가 다시 정렬됩니다.
- 참고용으로 타일 라벨에 좌표와 현재 타일 유형이 표시됩니다.

## 기술 세부 사항

- **단일 파일**: 의존성이나 빌드 단계가 없습니다. HTML 파일만 열면 됩니다.
- **그리드 시스템**: 타일 표시를 위해 CSS Grid 레이아웃을 사용합니다.
- **드로잉**: 마우스 누름(Mousedown) + 마우스 오버(Mouseover)로 드래그 앤 페인트 구현.
- **길찾기**: 자동 경로 생성에 BFS 알고리즘 사용.
- **시각화**: 경로 폴리라인(Polyline)에 SVG 오버레이 사용.
- **데이터 유지**: localStorage + cookie API 사용.
- **프레임워크 없음**: 외부 라이브러리 없이 순수 바닐라 자바스크립트(Vanilla JavaScript)로 작성.

## 브라우저 호환성

다음을 지원하는 모든 최신 모던 브라우저에서 작동합니다:
- ES6 JavaScript
- CSS Grid
- Fetch API
- localStorage
- Cookies

Chrome에서 테스트 완료.

---

**버전**: 1.0
**언어**: 한국어 UI 
**라이선스**: 
