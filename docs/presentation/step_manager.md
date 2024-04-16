# Explication du script StepManager

<div class="temp-card">
    Cette partie est en cours d'écriture
</div>

***

Dans le projet PolyLens, l'affichages des différentes étapes et animations sont gérés par le script `StepManager` qui suit :

!!! info

    Ce script est adapté pour l'utilisation de la [Localisation](/PolyLens-Doc/developpement/localisation/).

```c# linenums="1"
using Assets.SimpleLocalization.Scripts;
using TMPro;
using UnityEngine;

/// <summary>
/// Gère les étapes d'utilisation de la machine.
/// </summary>
public class StepManager : MonoBehaviour
{
    [Header("Texts UI")]
    [Tooltip("Texte affichant le titre de l'étape.")]
    [SerializeField] private TextMeshPro titleText;
    
    [Tooltip("Texte affichant la description de l'étape.")]
    [SerializeField] private TextMeshPro descriptionText;
    
    [Header("Buttons UI")]
    [Tooltip("Bouton pour passer à l'étape suivante.")]
    [SerializeField] private GameObject nextButton;
    
    [Tooltip("Bouton pour passer à l'étape précédente.")]
    [SerializeField] private GameObject previousButton;
    
    [Header("Fichier CSV")]
    [Tooltip("Fichier CSV contenant les étapes dans les différentes langues.")]
    [SerializeField] private TextAsset csvFile;
    
    [Header("Step List")]
    [Tooltip("Liste des étapes à activer lors de l'étape.")]
    [SerializeField] private GameObject[] stepGameObjects;
    
    private int currentStepIndex = 1;
    
    private int numberOfSteps;
    
    private void Start()
    {
        string[] lines = csvFile.text.Split('\n');
        numberOfSteps = lines.Length-1;
        UpdateUI();
    }
    
    /// <summary>
    /// Met à jour les textes de l'interface utilisateur.
    /// </summary>
    private void UpdateTextsUI()
    {
        titleText.text = "Étape " + currentStepIndex + " sur " + numberOfSteps;
        
        LocalizedTextTMP step = descriptionText.GetComponent<LocalizedTextTMP>();
        string stepKey = step.LocalizationKey;

        if (currentStepIndex <= 10)
        {
            stepKey = stepKey.Substring(0, stepKey.Length - 1);
        } else
        {
            stepKey = stepKey.Substring(0, stepKey.Length - 2);
        }
        
        stepKey += currentStepIndex;
        
        step.LocalizationKey = stepKey;
        descriptionText.text = LocalizationManager.Localize(stepKey);
    }

    /// <summary>
    /// Met à jour l'affichage des boutons de l'interface utilisateur.
    /// </summary>
    private void UpdateButtonUI()
    {
        previousButton.SetActive(true);
        nextButton.SetActive(true);
        
        if (currentStepIndex == 1) {
            previousButton.SetActive(false);
        } else if (currentStepIndex == numberOfSteps) {
            nextButton.SetActive(false);
        }
    }
    
    /// <summary>
    /// Met à jour l'affichage des gameobjects.
    /// </summary>
    private void UpdateGameObjects()
    {
        foreach (GameObject go in stepGameObjects) {
            go.SetActive(false);
        }
        
        stepGameObjects[currentStepIndex - 1].SetActive(true);
    }
    
    /// <summary>
    /// Met à jour l'interface utilisateur.
    /// </summary>
    private void UpdateUI()
    {
        UpdateTextsUI();
        UpdateButtonUI();
        UpdateGameObjects();
    }
    
    /// <summary>
    /// Permet de passer à l'étape suivante.
    /// </summary>
    public void NextStep()
    {
        if (currentStepIndex < numberOfSteps) {
            currentStepIndex++;
            UpdateUI();
        }
    }
    
    /// <summary>
    /// Permet de passer à l'étape précédente.
    /// </summary>
    public void PreviousStep()
    {
        if (currentStepIndex > 0) {
            currentStepIndex--;
            UpdateUI();
        }
    }
}

```

## Variables

```c# linenums="1"
// Texte qui affiche le titre de l'étape. C'est le texte dans la barre de titre de la SlateTuto.
private TextMeshPro titleText;

// Texte qui affiche la description de l'étape. C'est le texte dans le contenu de la SlateTuto.
private TextMeshPro descriptionText;

// Bouton pour passer à l'étape suivante. C'est le bouton ">" de la SlateTuto.
private GameObject nextButton;

// Bouton pour passer à l'étape précédente. C'est le bouton "<" de la SlateTuto.
private GameObject previousButton;

// Fichier CSV contenant les étapes dans les différentes langues. C'est le fichier CSV de la localisation de l'étape actuelle.
private TextAsset csvFile;

// Liste des étapes à activer lors de l'étape. C'est toutes les affichages et animations des étapes.
private GameObject[] stepGameObjects;

// Index courrant pour savoir à quelle étape on se situe.
private int currentStepIndex = 1;

// Nombre qui enregistre le nombre d'étape dans le fichier CSV.
private int numberOfSteps;
```

## Fonctions

```c# linenums="1"
// Fonction intégrer à Unity qui s'applique dès le démarrage.
private void Start()
{
    string[] lines = csvFile.text.Split('\n');  // Compte le nombre de ligne dans le fichier CSV
    numberOfSteps = lines.Length-1;             // Enregistre le nombre de ligne dans le fichier CSV
    UpdateUI();                                 // Met à jour l'UI
}

// Met à jour les textes de l'interface utilisateur.
private void UpdateTextsUI()
{
    // Met à jour le texte du titre de la SlateTuto
    titleText.text = "Étape " + currentStepIndex + " sur " + numberOfSteps;

    LocalizedTextTMP step = descriptionText.GetComponent<LocalizedTextTMP>();
    string stepKey = step.LocalizationKey;

    // Enlève les derniers chiffres de stepKey pour passer à l'étape suivante
    // Ex : Step5 -> Step -> Step6
    if (currentStepIndex <= 10)
    {
        stepKey = stepKey.Substring(0, stepKey.Length - 1);
    } else
    {
        stepKey = stepKey.Substring(0, stepKey.Length - 2);
    }

    stepKey += currentStepIndex;

    // Met à jour le texte
    step.LocalizationKey = stepKey;
    descriptionText.text = LocalizationManager.Localize(stepKey);
}

// Met à jour l'affichage des boutons de l'interface utilisateur.
private void UpdateButtonUI()
{
    // Active tout les boutons
    previousButton.SetActive(true);
    nextButton.SetActive(true);

    // Si première étape, désactive le bouton pour passer à l'étape d'avant
    if (currentStepIndex == 1) {
        previousButton.SetActive(false);
    // Si dernière étape, désactive le bouton pour passer à l'étape d'après
    } else if (currentStepIndex == numberOfSteps) {
        nextButton.SetActive(false);
    }
}

// Met à jour l'affichage des gameobjects.
private void UpdateGameObjects()
{
    // Désatcive tout
    foreach (GameObject go in stepGameObjects) {
        go.SetActive(false);
    }

    // Active l'affichage de l'étape courrante
    stepGameObjects[currentStepIndex - 1].SetActive(true);
}

// Met à jour l'interface utilisateur.
private void UpdateUI()
{
    UpdateTextsUI();
    UpdateButtonUI();
    UpdateGameObjects();
}

// Permet de passer à l'étape suivante.
public void NextStep()
{
    if (currentStepIndex < numberOfSteps) {
        currentStepIndex++;
        UpdateUI();
    }
}

// Permet de passer à l'étape précédente.
public void PreviousStep()
{
    if (currentStepIndex > 0) {
        currentStepIndex--;
        UpdateUI();
    }
}
```