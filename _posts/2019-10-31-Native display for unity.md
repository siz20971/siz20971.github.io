---
layout: post
title: 멀티 모니터에 대한 유니티 클라이언트 모니터 이동
categories: Development, Unity
---

유니티에서 멀티 모니터간의 윈도우 이동 기능은 지원되지 않으므로, 새로 만듬.

```c#
#if UNITY_STANDALONE_WIN || UNITY_EDITOR_WIN

using System.Runtime.InteropServices;
using System;
using System.Collections.Generic;
using AOT;

public static class UnityNativeDisplays
{
    // !! DO NOT Change variable order !!
    public struct DisplayRect
    {
        public int left;
        public int top;
        public int right;
        public int bottom;

        public int width { get { return (right - left); } }
        public int height { get { return (bottom - top); } }
    }

    public delegate int MONITORENUMPROC(IntPtr hMonitor, IntPtr hdcMonitor, ref DisplayRect lprcMonitor, Int64 dwData);

    [DllImport("user32.dll", EntryPoint = "SetWindowPos")]
    private static extern bool SetWindowPos(IntPtr hwnd, int hWndInsertAfter, int x, int Y, int cx, int cy, int wFlags);

    //[DllImport("user32.dll", EntryPoint = "FindWindow")]
    //public static extern IntPtr FindWindow(String className, String windowName);

    [DllImport("user32.dll")]
    static extern IntPtr GetActiveWindow();

    [DllImport("user32.dll", EntryPoint = "GetDC")]
    public static extern IntPtr GetDC(IntPtr hWnd);

    [DllImport("user32.dll", EntryPoint = "ReleaseDC")]
    public static extern IntPtr ReleaseDC(IntPtr hWnd, IntPtr hDC);

    [DllImport("user32.dll", EntryPoint = "GetWindowRect")]
    public static extern bool GetWindowRect(IntPtr hwnd, ref DisplayRect rect);

    [DllImport("user32.dll", EntryPoint = "EnumDisplayMonitors")]
    public static extern int EnumDisplayMonitors(IntPtr hdc, IntPtr lprcClip, MONITORENUMPROC lpfnEnum, Int64 dwData);

    private static List<DisplayRect> displayRects = new List<DisplayRect>();

    [MonoPInvokeCallback(typeof(MONITORENUMPROC))]
    static int MonitorEnumCallback(IntPtr hMonitor, IntPtr hdcMonitor, ref DisplayRect lprcMonitor, Int64 dwData)
    {
        DisplayRect rc = new DisplayRect();
        rc.left     = lprcMonitor.left;
        rc.top      = lprcMonitor.top;
        rc.right    = lprcMonitor.right;
        rc.bottom   = lprcMonitor.bottom;
        displayRects.Add(rc);

        return 1;
    }

#region PUBLIC
    public static int NumOfDisplay
    {
        get
        {
            DetectNativeDisplays();
            return displayRects.Count;
        }
    }

    /// <summary>
    /// switch window to index's display rect's center
    /// </summary>
    /// <param name="index">display index</param>
    /// <param name="ignoreIfWindowInDisplay">If current window in index's display rect, ignore switch</param>
    public static void SwitchToDisplay(int index, bool ignoreIfWindowInDisplay = true)
    {
        if (displayRects == null)
            DetectNativeDisplays();

        if (displayRects.Count == 0)
            return;

        if (index < 0 || index >= displayRects.Count)
            return;

        var dpRect = displayRects[index];
        IntPtr hwnd = GetActiveWindow();

        if (hwnd == IntPtr.Zero)
            return;

        DisplayRect winRect = new DisplayRect();
        GetWindowRect(hwnd, ref winRect);

        if (ignoreIfWindowInDisplay && winRect.IsInRect(ref dpRect))
            return;

        // to center of display
        int left = dpRect.left + (int)((dpRect.width - winRect.width) * 0.5f);
        int top = dpRect.top + (int)((dpRect.height - winRect.height) * 0.5f);

        SetWindowPos(hwnd, 0, left, top, winRect.width, winRect.height, 0);
    }

    public static void DetectNativeDisplays()
    {
        if (displayRects == null)
            displayRects = new List<DisplayRect>();

        displayRects.Clear();

        IntPtr hwnd = (IntPtr)0;
        IntPtr hdc = GetDC(hwnd);

        EnumDisplayMonitors(hdc, (IntPtr)0, MonitorEnumCallback, 0);
        ReleaseDC(hwnd, hdc);
    }
#endregion

    private static bool IsInRect(this DisplayRect selfRect, ref DisplayRect rect)
    {
        return (selfRect.left >= rect.left
           && selfRect.right <= rect.right
           && selfRect.bottom <= rect.bottom
           && selfRect.top >= rect.top);
    }
}
#endif
```