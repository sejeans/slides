# slides

프로젝트의 코드, 문서, 결과 파일을 읽고 발표용 슬라이드 원고를 만드는 Claude Code 스킬입니다. 원고는 마크다운(`slides.md`)으로 나오고, 디자인은 Figma 플러그인 Deck Builder가 고정된 규칙으로 그립니다.

## 만들어지는 것

| 파일 | 내용 |
|---|---|
| `slides/slides.md` | 슬라이드 원고 |
| `slides/assets/` | 차트 PNG, 사진, 스크린샷 |
| `slides/charts/*.py` | 차트를 재생성하는 스크립트 |

기본 형식은 보고서형입니다. 발표자가 없어도 슬라이드만 읽고 내용을 이해할 수 있도록 내용을 빠짐없이 담고, 문단 대신 개조식 항목으로 구조화합니다. 디자인은 논문 발표형(남색 띠, 주황 강조)이 기본이며, 표지, 목차, 섹션, 내용, 마무리 장이 자동으로 구성됩니다.

## 설치

```bash
git clone https://github.com/sejeans/slides ~/.claude/skills/slides
```

필요한 환경은 다음과 같습니다.

- Node.js: 원고 점검(`check.js`)
- Python과 matplotlib: 차트 생성(`slidechart.py`)
- Figma의 Deck Builder 플러그인: 원고를 슬라이드로 변환

## 사용

Claude Code에서 프로젝트 폴더를 연 뒤 아래처럼 요청하면 스킬이 실행됩니다.

- "발표 자료 만들어줘"
- "슬라이드 구성 짜줘"
- "발표용으로 정리해줘"
- "차트 넣어줘"

진행 순서는 다음과 같습니다.

1. README, 문서, 결과 파일, 핵심 코드를 읽습니다.
2. 섹션 구성과 슬라이드 제목을 먼저 보여 줍니다.
3. `slides/slides.md`와 차트를 작성합니다. 수치는 모두 프로젝트 파일에서 가져오고 `source:`에 출처를 적습니다.
4. `check.js`로 점검합니다. 통과할 때까지 수정합니다.
5. Figma에서 Plugins, Development, Deck Builder를 열고 `slides.md`를 불러온 뒤 `slides/assets`의 파일을 끌어 놓습니다.

## 원고 작성 규칙

- 슬라이드마다 핵심 메시지(`>`)를 씁니다. 주제가 아니라 결론을 90자 이내로 적습니다.
- 본문은 개조식(`~함`, `~임`)으로 쓰고, 슬라이드당 600자 안팎으로 유지합니다.
- 표와 그림에는 무엇을 봐야 하는지 적습니다. 볼 행은 `highlight: n`, 볼 칸은 `==값==`으로 표시하고, 해석은 `→` 줄에 씁니다.
- 강조는 `**남색 굵게**`와 `==주황 굵게==` 두 가지만 씁니다.
- 상투어(혁신적, 획기적, 시너지 등)와 이모지는 쓰지 않습니다.

도식은 Figma 도형으로 그려지므로 생성한 뒤에도 글자와 위치를 직접 고칠 수 있습니다.

| 내용 | 문법 |
|---|---|
| 절차, 인과 | ```` ```flow ```` (Mermaid flowchart와 같은 문법) |
| A와 B 비교 | ```` ```vs ```` |
| 전과 후의 변화 | ```` ```change ```` |

문법 전체는 [SKILL.md](SKILL.md)에 있습니다.

## 구성

```
slides/
├── SKILL.md        스킬 본문 (작성 규칙과 문법 전체)
├── check.js        원고 점검
├── core.js         원고 해석 공통 코드
├── slidechart.py   차트 스타일
└── example/        완성 예시 (slides.md, diagrams.md, 차트와 사진)
```

처음 사용한다면 `example/slides.md`와 `example/diagrams.md`를 먼저 읽는 것을 권장합니다.

## 점검

```bash
node ~/.claude/skills/slides/check.js slides/slides.md slides/assets
```

`점검 통과`가 출력되면 완료입니다. 점검 항목은 핵심 메시지 누락, 표 강조 누락, 줄글과 과밀, 흐름도 단계 수, 상투어, 빠진 사진입니다.

## 차트

```python
import sys; sys.path.insert(0, "~/.claude/skills/slides")
import slidechart as sc

fig, ax = sc.figure("col")   # wide: 본문 전체 폭, col: 2단 중 한쪽
sc.bar(ax, labels, values, emphasize=[best_idx], fmt="{:.0f}%")
sc.save(fig, "slides/assets/accuracy.png")
```

회색을 기본으로 하고 강조할 대상 하나만 남색으로 표시합니다. 차트 안에는 제목을 넣지 않으며, 범례 대신 선 끝에 이름을 씁니다.
