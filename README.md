---
description: 202201543 최경민
---

# LINUX시스템 과목 기말대체과제

2023학년도 LINUX시스템 과목 기말대체과제용 Gitbook입니다. 

## Gitbook?
협업을 위한 문서 작성 플랫폼으로 업무 가이드라인, API 명세서 작성 등의 기본 템플릿을 제공한다. 뭣보다 마크다운으로 간편하게 책과 같은 형테로 문서를 만들어낼 수 있어 개발문서 작성에 많이 이용한다.

## PDF Export
Gitbook CLI를 거의 방치하고 수익을 내기 위해 SaaS로 전환해 최신 버전의 노드에 대응되지 않는다. 따라서 node 16.18.0 버전을 이용해야 한다. 오픈소스 프로젝트의 맹점을 확인할 수 있었다.
```bash
npm install -g gitbook
# 전자책 도구 calibre 설치 및 패키지에 포함된 ebook-convert CLI 도구 /usr/local/bin에 심볼릭 링크 생성
ln -s /Applications/calibre.app/Contents/MacOS/ebook-convert /usr/local/bin/
gitbook pdf
```

