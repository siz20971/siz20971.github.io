---
layout: post
title: 유니티 PreProcessBuild에서 스크립트 파일 명을 바꾸는 경우
categories: Development, Unity, BuildPipeline
---

유니티 빌드 전후로 추가적인 작업들을 아래와 같이 할 수 있다.

```c#
public class PreprocessExample : IPreprocessBuildWithReport, IPostprocessBuildWithReport
{
    public int callbackOrder    {   get {   return 0;   }   }

    public void OnPreprocessBuild(BuildReport report)
    {
        RenameScripts();
    }

    public void OnPostprocessBuild(BuildReport report)
    {
        RestoreScripts();
    }
}
```

그런데 위 함수 이름처럼 스크립트의 파일 명을 바꾸는 경우, 100% 에러가 발생한다.

에러 내용을 보면 바뀐 스크립트를 컴파일러가 찾지 못해서 

```
-----CompilerOutput:-stdout--exitcode: 1--compilationhadfailure: True--outfile: Temp/Assembly-CSharp.dll
Microsoft (R) Visual C# Compiler version 2.9.1.65535 (9d34608e)
Copyright (C) Microsoft Corporation. All rights reserved.
```

대충 이런 로그와 함께 변경된 스크립트들을 찾지 못했다는 에러가 발생한다.

```c#
public class PreprocessExample : IPreprocessBuildWithReport, IPostprocessBuildWithReport
{
    public int callbackOrder    {   get {   return 0;   }   }

    public void OnPreprocessBuild(BuildReport report)
    {
        RenameScripts();
        AssetDatabase.Refresh();
    }

    public void OnPostprocessBuild(BuildReport report)
    {
        RestoreScripts();
        AssetDatabase.Refresh();
    }
}
```

이 경우 AssetDatabase.Refresh(); 추가해주면, AssetDatabase가 갱신되면서 빌드가 정상적으로 될... 줄알았는데, 뭔가 다른 에러가 났다.

시간이 없어서 제대로 확인은 못했지만 같이 사용하고 있던 Unity Obfuscator의 문제인지 아닌지 모르겠다.

아무튼 코드 난독화 처리를 위해 추가작업을 했지만, 유니티 빌드파이프라인 중간에 넣기엔 좀 위험해 보인다.

애초에 이런식으로 무식한 방법으로 클래스 이름 치환을 하는것도 좀 그렇긴 한데...

아무튼 클래스 파일 및 이름 단순 치환을 하려면 그냥 빌드 시작전에 다른 스크립트 언어로 돌리는게 낫지 않나 싶다.

일단 빠르게 필요해서 별개 프로젝트를 만들어서 프로그램 돌리면 변환하도록 해둠.