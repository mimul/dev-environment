Mac 저장공간 확보하기
======================
Mac에서 개발자가 자주 사용하는 앱들에 대해서 설치, 설정 정보들을 공유한다.

### 대용량 파일 확인
아래 명령어를 사용하여 큰 파일을 선택적으로 찾아서 지운다.
```
# 루트에서 15개의 하위계층까지 500MB 이상의 파일 찾기
> sudo du -x -m -d 15 / | awk '$1 >= 500{print}'

# 현재 디렉토리에서 50MB 이상의 파일 찾기
> du -x -m | awk '$1 >= 50{print}'
```

### 특정 디렉토리 확인 후 삭제
```
/System/Library/Caches
/System/Volumes/Data/.fseventsd

/Library/Caches
/Library/Logs

~/.cache
~/Library/Caches
~/Library/Logs
~/Library/Metadata
```

### 앱 관련 디렉토리 확인 후 삭제

**Google Chrome**
```
~/Library/Application\ Support/Google/Chrome/Snapshots
~/Library/Application\ Support/Google/Chrome/Profile\ 1/Service Worker/CacheStorage
```

**MS Office**
```
~/Library/Group\ Containers/UBF8T346G9.Office/FontCache
```

**XCode**
```
~/Library/Developer/CoreSimulator/Caches
~/Library/Developer/Xcode/DerivedData
```

**Docker**
```
> docker system prune -a --filter "until=24h"
```

**VSCode**
```
~/Library/Application\ Support/Code/Cache
~/Library/Application\ Support/Code/CachedData
~/Library/Application\ Support/Code/Crashpad
~/Library/Application\ Support/Code/CachedExtensionVSIXs
~/Library/Application\ Support/Code/Service\ Worker/CacheStorage
~/Library/Application\ Support/Code/WebStorage/13/CacheStorage
```

**Slack**
```
~/Library/Application\ Support/Slack/Cache
~/Library/Application\ Support/Slack/Code\ Cache
~/Library/Application\ Support/Slack/logs
~/Library/Application\ Support/Slack/Service\ Worker/CacheStorage
```

**Discord**
```
~/Library/Application\ Support/discord/Cache
~/Library/Application\ Support/discord/Code\ Cache
```
