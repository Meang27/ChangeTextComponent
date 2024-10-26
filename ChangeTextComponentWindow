using UnityEditor;
using UnityEngine;
using UnityEngine.UI;
using TMPro;
using UnityEditor.SceneManagement;
using System.Collections.Generic;
using System.Linq;

public class UIData
{
    public string text;                     // 문자열 정보

    public TMP_FontAsset fontAsset;         // tmp 폰트 정보
    public Font font;                       // text 폰트 정보

    public FontStyles fontStyles;           // tmp 폰트 스타일 정보
    public FontStyle fontStyle;             // text 폰트 스타일 정보

    public int fontSize;                    // 폰트 크기 정보

    public TextAlignmentOptions alignment;  // tmp 폰트 정렬 정보
    public TextAnchor textAnchor;           // text 폰트 정렬 정보

    public Color color;                     // 폰트 색깔 정보
}

public enum eUIType
{
    TEXT,
    TEXT_MESH_PRO
}

public class ChangeTextComponentWindow : EditorWindow
{
    #region [private Variables]
    /// <summary>선택한 Prefab</summary>
    private GameObject m_PrefabUI;
    /// <summary>선택한 Prefab 경로</summary>
    private string m_PrefabPath = string.Empty;

    /// <summary>선택한 Prefab에서 찾은 TextUI 리스트</summary>
    private List<Text> m_Texts;
    /// <summary>선택한 Prefab에서 찾은 TextUI 개수</summary>
    private int m_TextCount;

    /// <summary>선택한 Prefab에서 찾은 TextMeshPro 리스트</summary>
    private List<TextMeshProUGUI> m_TextMeshProGUIs;
    /// <summary>선택한 Prefab에서 찾은 TextMeshPro 개수</summary>
    private int m_TMPCount;

    /// <summary>Prefab Mode로 활성화된 Prefab</summary>
    private GameObject m_PrefabMode;

    /// <summary>현재 Prefab 단계를 가져옴</summary>
    private PrefabStage m_NewPrefab;

    private Vector2 m_ScrollPos = Vector2.zero;

    /// <summary>UI 타입</summary>
    private eUIType m_UiType;

    /// <summary>선택한 UI Type => Text</summary>
    private bool m_IsText = false;
    /// <summary>선택한 UI Type => TMP</summary>
    private bool m_IsTMP = false;
    #endregion

    [MenuItem("Tools/UI Type/Text OR TextMeshPro")]
    static void ShowEditor()
    {
        ChangeTextComponentWindow window = GetWindow<ChangeTextComponentWindow>();
        window.titleContent = new GUIContent("Text & TMP Component Change");
        window.minSize = new Vector2(520f, 500f);
        window.maxSize = new Vector2(520f, 700f);
        window.Init();
        window.ShowUtility();
    }

    /// <summary>
    /// 초기화
    /// </summary>
    private void Init()
    {
        m_PrefabUI = null;
        if (m_Texts != null)
        {
            m_Texts.Clear();
        }
        else
        {
            m_Texts = new List<Text>();
        }

        if (m_TextMeshProGUIs != null)
        {
            m_TextMeshProGUIs.Clear();
        }
        else
        {
            m_TextMeshProGUIs = new List<TextMeshProUGUI>();
        }
    }

    private void OnGUI()
    {
        _DrawLine(5);

        // 변경할 Prefab 선택
        _SelectPrefab();

        GUILayout.Space(10);
        _DrawLine(5);

        // 원하는 UI Type 찾기
        _FindUIType();

        GUILayout.Space(10);
        _DrawLine(5);

        // 모든 UI Type 변경
        _ChangeAllUIType();

        GUILayout.Space(10);
        _DrawLine(5);

        // 선택한 UI Type별 찾은 목록
        _FindObjectResult();

        GUILayout.Space(10);
        _DrawLine(5);

        // 변경사항 저장
        _SaveChanges();

        GUILayout.Space(10);
    }

    /// <summary>
    /// 라인 그리기
    /// </summary>
    private void _DrawLine(int value)
    {
        GUILayout.Space(value);
        var rect = EditorGUILayout.BeginHorizontal();
        Handles.color = Color.white;
        Handles.DrawLine(new Vector2(rect.x, rect.y), new Vector2(rect.width, rect.y));
        EditorGUILayout.EndHorizontal();
        GUILayout.Space(value);
    }

    /// <summary>
    /// 변경할 프리팹 선택
    /// </summary>
    private void _SelectPrefab()
    {
        GUILayout.Label("1. 변경할 Prefab을 드래그", EditorStyles.boldLabel);
        GUILayout.Space(10);
        EditorGUILayout.BeginHorizontal();
        m_PrefabUI = EditorGUILayout.ObjectField("Prefab", m_PrefabUI, typeof(GameObject), true) as GameObject;
        EditorGUILayout.EndHorizontal();
    }

    /// <summary>
    /// 프리팹 모드에서 UI Type별 개수 찾기
    /// </summary>
    private void _FindUIType()
    {
        GUILayout.Label("2. 원하는 UI Type으로 찾기", EditorStyles.boldLabel, GUILayout.Width(520f));
        GUILayout.Space(10);

        if (m_PrefabUI == null && m_NewPrefab == null)
            return;

        GUILayout.BeginHorizontal();
        if (GUILayout.Button("Text UI 찾기", GUILayout.Width(255)))
        {
            m_PrefabPath = AssetDatabase.GetAssetPath(m_PrefabUI);

            // 프리팹 모드로 전환 후 Text UI 개수 셋팅
            PrefabStageUtility.OpenPrefab(m_PrefabPath);
            m_NewPrefab = PrefabStageUtility.GetCurrentPrefabStage();

            m_PrefabMode = m_NewPrefab.prefabContentsRoot;

            Text[] texts = m_PrefabMode.GetComponentsInChildren<Text>(true);
            m_Texts = texts.ToList();

            if (m_PrefabMode != null)
            {
                m_UiType = eUIType.TEXT;
                m_IsText = true;
                m_IsTMP = false;
            }
            m_TextCount = m_Texts.Count;
        }

        if (GUILayout.Button("TextMeshPro UI 찾기", GUILayout.Width(255)))
        {
            m_PrefabPath = AssetDatabase.GetAssetPath(m_PrefabUI);

            // 프리팹 모드로 전환 후 TextMeshPro UI 개수 셋팅
            PrefabStageUtility.OpenPrefab(m_PrefabPath);
            m_NewPrefab = PrefabStageUtility.GetCurrentPrefabStage();

            m_PrefabMode = m_NewPrefab.prefabContentsRoot;

            TextMeshProUGUI[] textMeshProGUIs = m_PrefabMode.GetComponentsInChildren<TextMeshProUGUI>(true);
            m_TextMeshProGUIs = textMeshProGUIs.ToList();

            if (m_PrefabMode != null)
            {
                m_UiType = eUIType.TEXT_MESH_PRO;
                m_IsText = false;
                m_IsTMP = true;
            }
            m_TMPCount = m_TextMeshProGUIs.Count;
        }
        GUILayout.EndHorizontal();

        GUILayout.BeginHorizontal();
        m_TextCount = EditorGUILayout.IntField("찾은 Text 개수", m_TextCount);
        m_TMPCount = EditorGUILayout.IntField("찾은 TextMeshPro 개수", m_TMPCount);
        GUILayout.EndHorizontal();
    }

    /// <summary>
    /// 원하는 UI Type으로 모든 UI 변경
    /// </summary>
    private void _ChangeAllUIType()
    {
        GUILayout.Label("3. 모든 UI Type변경", EditorStyles.boldLabel, GUILayout.Width(520f));
        GUILayout.Space(10);

        if (m_PrefabUI == null || m_NewPrefab == null)
            return;

        GUILayout.BeginHorizontal();
        if (m_UiType == eUIType.TEXT)
        {
            if (GUILayout.Button("Text -> TextMeshPro 변환", GUILayout.ExpandWidth(true)))
            {
                _ChangeAllTextMeshProGUI();
                m_Texts.Clear();
                _ResetCountData();
            }
        }

        else if (m_UiType == eUIType.TEXT_MESH_PRO)
        {
            if (GUILayout.Button("TextMeshPro -> Text 변환", GUILayout.ExpandWidth(true)))
            {
                _ChangeAllTextGUI();

                m_TextMeshProGUIs.Clear();
                _ResetCountData();
            }
        }
        GUILayout.EndHorizontal();
    }

    /// <summary>
    /// 모든 Text 정보 => TextMeshPro 정보로 모두 변경 
    /// </summary>
    private void _ChangeAllTextMeshProGUI()
    {
        foreach (Text text in m_Texts)
        {
            UIData data = _TextData(text);
            GameObject obj = text.gameObject;
            DestroyImmediate(text);
            if (!obj.GetComponent<TextMeshProUGUI>())
            {
                TextMeshProUGUI newTMP = obj.AddComponent<TextMeshProUGUI>();

                newTMP.text = data.text;
                newTMP.font = data.fontAsset;
                newTMP.fontStyle = data.fontStyles;
                newTMP.fontSize = data.fontSize;
                newTMP.alignment = data.alignment;
                newTMP.color = data.color;
            }

            if (obj.name.Contains("_TEXT"))
            {
                obj.name = obj.name.Replace("_TEXT", "_TMP");
            }
            else
            {
                obj.name += "_TMP";
            }
        }
    }

    /// <summary>
    /// 모든 TextMeshPro => Text 정보로 모두 변경
    /// </summary>
    private void _ChangeAllTextGUI()
    {
        foreach (TextMeshProUGUI tmp in m_TextMeshProGUIs)
        {
            UIData data = _TextMeshProData(tmp);
            GameObject obj = tmp.gameObject;
            DestroyImmediate(tmp);
            if (!obj.GetComponent<Text>())
            {
                Text newText = obj.AddComponent<Text>();

                newText.text = data.text;
                newText.font = data.font;
                newText.fontStyle = data.fontStyle;
                newText.fontSize = data.fontSize;
                newText.alignment = data.textAnchor;
                newText.color = data.color;
            }

            if (obj.name.Contains("_TMP"))
            {
                obj.name = obj.name.Replace("_TMP", "_TEXT");
            }
            else
            {
                obj.name += "_TEXT";
            }
        }
    }

    /// <summary>
    /// Text 정보 => TextMeshPro 정보로 변경 (개별 변경)
    /// </summary>
    private void _ChangeTextMeshProGUI(Text text)
    {
        UIData data = _TextData(text);
        GameObject obj = text.gameObject;
        DestroyImmediate(text);
        if (!obj.GetComponent<TextMeshProUGUI>())
        {
            TextMeshProUGUI newTMP = obj.AddComponent<TextMeshProUGUI>();

            newTMP.text = data.text;
            newTMP.font = data.fontAsset;
            newTMP.fontStyle = data.fontStyles;
            newTMP.fontSize = data.fontSize;
            newTMP.alignment = data.alignment;
            newTMP.color = data.color;
        }

        if (obj.name.Contains("_TEXT"))
        {
            obj.name = obj.name.Replace("_TEXT", "_TMP");
        }
        else
        {
            obj.name += "_TMP";
        }
    }

    /// <summary>
    /// TextMeshPro => Text 정보로 변경 (개별 변경)
    /// </summary>
    private void _ChangeTextGUI(TextMeshProUGUI tmp)
    {
        UIData data = _TextMeshProData(tmp);
        GameObject obj = tmp.gameObject;
        DestroyImmediate(tmp);
        if (!obj.GetComponent<Text>())
        {
            Text newText = obj.AddComponent<Text>();

            newText.text = data.text;
            newText.font = data.font;
            newText.fontStyle = data.fontStyle;
            newText.fontSize = data.fontSize;
            newText.alignment = data.textAnchor;
            newText.color = data.color;
        }

        if (obj.name.Contains("_TMP"))
        {
            obj.name = obj.name.Replace("_TMP", "_TEXT");
        }
        else
        {
            obj.name += "_TEXT";
        }
    }

    /// <summary>
    /// 찾은 UI Type별 목록 보여주기 및 개별 UI Type 변경 버튼 활성화
    /// </summary>
    private void _FindObjectResult()
    {
        GUILayout.Label("3 - 1. 개별 UI Type변경", EditorStyles.boldLabel, GUILayout.Width(520f));
        GUILayout.Space(15);

        if (m_PrefabUI == null || m_NewPrefab == null)
            return;

        if (m_Texts != null && m_IsText)
        {
            m_ScrollPos = EditorGUILayout.BeginScrollView(m_ScrollPos);

            for (int i = 0; i < m_TextCount; i++)
            {
                if (m_Texts[i] == null)
                    return;

                GUILayout.BeginHorizontal();
                if (GUILayout.Button(m_Texts[i].gameObject.name + " "+ "[" + m_UiType + "]", GUILayout.Width(390f)))
                {
                    Selection.activeGameObject = m_Texts[i].gameObject;
                }

                if (GUILayout.Button("UI Type 변경", GUILayout.Width(120f)))
                {
                    _ChangeTextMeshProGUI(m_Texts[i]);
                    // 개수 초기화
                    _ResetCountData();
                }
                GUILayout.EndHorizontal();
            }
            EditorGUILayout.EndScrollView();
        }

        if (m_TextMeshProGUIs != null && m_IsTMP)
        {
            m_ScrollPos = EditorGUILayout.BeginScrollView(m_ScrollPos);

            for (int i = 0; i < m_TMPCount; i++)
            {
                if (m_TextMeshProGUIs[i] == null)
                    return;

                GUILayout.BeginHorizontal();
                if (GUILayout.Button(m_TextMeshProGUIs[i].gameObject.name + " " + "[" + m_UiType + "]", GUILayout.Width(390f)))
                {
                    Selection.activeGameObject = m_TextMeshProGUIs[i].gameObject;
                }

                if (GUILayout.Button("UI Type 변경", GUILayout.Width(120f)))
                {
                    _ChangeTextGUI(m_TextMeshProGUIs[i]);
                    // 개수 초기화
                    _ResetCountData();
                }
                GUILayout.EndHorizontal();
            }
            EditorGUILayout.EndScrollView();
        }
    }

    /// <summary>
    /// Text, TMP 개수 초기화
    /// </summary>
    private void _ResetCountData()
    {
        Text[] texts = m_PrefabMode.GetComponentsInChildren<Text>(true);
        m_Texts = texts.ToList();
        m_TextCount = m_Texts.Count;

        TextMeshProUGUI[] textMeshProGUIs = m_PrefabMode.GetComponentsInChildren<TextMeshProUGUI>(true);
        m_TextMeshProGUIs = textMeshProGUIs.ToList();
        m_TMPCount = m_TextMeshProGUIs.Count;
    }

    /// <summary>
    /// 프리팹 저장 완료 상황 보여주기
    /// </summary>
    private void _ShowResult()
    {
        bool isResult = EditorUtility.DisplayDialog("결과", "저장이 완료되었습니다.", "확인");
        if (isResult)
        {
            // 프리팹 제거
            m_PrefabUI = null;
            m_PrefabPath = null;
            m_PrefabMode = null;

            // 값 초기화
            m_Texts.Clear();
            m_TextCount = 0;
            m_TextMeshProGUIs.Clear();
            m_TMPCount = 0;
        }
    }

    /// <summary>
    /// 프리팹 변경사항 저장
    /// </summary>
    private void _SaveChanges()
    {
        GUIStyle style = new GUIStyle(GUI.skin.label);
        style.normal.textColor = Color.yellow;
        style.fontStyle = FontStyle.Bold;

        GUILayout.Label("4. 프리팹 저장 (저장을 하지 않으면 저장이 되지 않습니다.)", style, GUILayout.Width(520f));
        GUILayout.Space(10);

        if (m_PrefabUI == null || m_NewPrefab == null)
            return;

        GUILayout.BeginHorizontal();

        if (GUILayout.Button("변경사항 저장", GUILayout.ExpandWidth(true)))
        {
            // 변경된 프리팹 저장
            PrefabUtility.SaveAsPrefabAsset(m_PrefabMode, m_PrefabPath);
            _ShowResult();
        }
        GUILayout.EndHorizontal();
    }

    /// <summary>
    /// Text 정보 저장
    /// </summary>
    private UIData _TextData(Text text)
    {
        UIData data = new UIData();
        data.text = text.text;
        data.fontSize = text.fontSize;
        data.color = text.color;
#if UNITY_EDITOR
        data.fontAsset = AssetDatabase.LoadAssetAtPath<TMP_FontAsset>("Assets/Fonts/KOTRA HOPE SDF.asset");
#endif
        switch (text.fontStyle)
        {
            case FontStyle.Bold:
                data.fontStyles = FontStyles.Bold;
                break;
            case FontStyle.Italic:
            case FontStyle.BoldAndItalic:
                data.fontStyles = FontStyles.Italic;
                break;
            default:
                data.fontStyles = FontStyles.Normal;
                break;
        }

        switch (text.alignment)
        {
            case TextAnchor.UpperLeft:
                data.alignment = TextAlignmentOptions.TopLeft;
                break;
            case TextAnchor.UpperCenter:
                data.alignment = TextAlignmentOptions.Top;
                break;
            case TextAnchor.UpperRight:
                data.alignment = TextAlignmentOptions.TopRight;
                break;
            case TextAnchor.MiddleLeft:
                data.alignment = TextAlignmentOptions.Left;
                break;
            case TextAnchor.MiddleCenter:
                data.alignment = TextAlignmentOptions.Center;
                break;
            case TextAnchor.MiddleRight:
                data.alignment = TextAlignmentOptions.Right;
                break;
            case TextAnchor.LowerLeft:
                data.alignment = TextAlignmentOptions.BottomLeft;
                break;
            case TextAnchor.LowerCenter:
                data.alignment = TextAlignmentOptions.Bottom;
                break;
            case TextAnchor.LowerRight:
                data.alignment = TextAlignmentOptions.BottomRight;
                break;
        }
        return data;
    }

    /// <summary>
    /// TMP 정보 저장
    /// </summary>
    private UIData _TextMeshProData(TextMeshProUGUI tmp)
    {
        UIData data = new UIData();
        data.text = tmp.text;
        data.fontSize = (int)tmp.fontSize;
        data.color = tmp.color;
#if UNITY_EDITOR
        data.font = AssetDatabase.LoadAssetAtPath<Font>("Assets/Fonts/KOTRA HOPE.ttf");
#endif
        switch (tmp.fontStyle)
        {
            case FontStyles.Bold:
                data.fontStyle = FontStyle.Bold;
                break;
            case FontStyles.Italic:
                data.fontStyle = FontStyle.Italic;
                break;
            default:
                data.fontStyle = FontStyle.Normal;
                break;
        }

        switch (tmp.alignment)
        {
            case TextAlignmentOptions.TopLeft:
                data.textAnchor = TextAnchor.UpperLeft;
                break;
            case TextAlignmentOptions.Top:
                data.textAnchor = TextAnchor.UpperCenter;
                break;
            case TextAlignmentOptions.TopRight:
                data.textAnchor = TextAnchor.UpperRight;
                break;
            case TextAlignmentOptions.Left:
                data.textAnchor = TextAnchor.MiddleLeft;
                break;
            case TextAlignmentOptions.Center:
                data.textAnchor = TextAnchor.MiddleCenter;
                break;
            case TextAlignmentOptions.Right:
                data.textAnchor = TextAnchor.MiddleRight;
                break;
            case TextAlignmentOptions.BottomLeft:
                data.textAnchor = TextAnchor.LowerLeft;
                break;
            case TextAlignmentOptions.Bottom:
                data.textAnchor = TextAnchor.LowerCenter;
                break;
            case TextAlignmentOptions.BottomRight:
                data.textAnchor = TextAnchor.LowerRight;
                break;
        }
        return data;
    }
}
