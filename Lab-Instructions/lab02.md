
# LAB02 Analyze Images with Azure AI Vision

Azure AI Vision is an artificial intelligence capability that enables software systems to interpret visual input by analyzing images. In Microsoft Azure, the **Vision** Azure AI service provides pre-built models for common computer vision tasks, including analysis of images to suggest captions and tags, detection of common objects and people. You can also use the Azure AI Vision service to remove the background or create a foreground matting of images.


## Provision an Azure AI Services resource

If you don't already have one in your subscription, you'll need to provision an **Azure Computer Vision** resource.

1. Open the Azure portal at `https://portal.azure.com`, and sign in using the Microsoft account associated with your Azure subscription.
2. Select **Create a resource**.
3. In the search bar, search for *Computer Vision*, select **Computer Vision**, and create a Computer Vision resource with the following settings:
    - **Subscription**: *Your Azure subscription*
    - **Resource group**: *Choose your resource group 
    - **Region**: *Choose from East US, West US, France Central, Korea Central, North Europe, Southeast Asia, West Europe, or East Asia\**
    - **Name**: *Enter a unique name*
    - **Pricing tier**: Standard S1

    \*Azure AI Vision 4.0 full feature sets are currently only available in these regions.

4. Select the required checkboxes, leave rest with default options and create the resource.
5. Wait for deployment to complete, and then view the deployment details.
6. When the resource has been deployed, go to it and view its **Keys and Endpoint** page. You will need the endpoint and one of the keys from this page in the next procedure.

## Prepare to use the Azure AI Vision SDK

In this exercise, you'll complete a partially implemented client application that uses the Azure AI Vision SDK to analyze images.


1. From the root page of the repository, launch the same GitHub Codespace from lab01 (or reopen if still running). https://github.com/unaihuete93/Tecnun-AIFun-2024 

1. In Github Codespace, in the **Explorer** pane, browse to the **Lab-Files/Lab02** folder and expand the  folder.
2. Right-click on the folder and open an integrated terminal. Then install the Azure AI Vision SDK package:


    **Python**
    
    ```
    pip install azure-ai-vision-imageanalysis==1.0.0b3
    ```

    > **Tip**: If you are doing this lab on your own machine, you'll also need to install `matplotlib` and `pillow`.
    
3. View the contents of the **Lab02** folder, and note that it contains a file for configuration settings:
   
    - **Python**: .env

    Open the configuration file and update the configuration values it contains to reflect the **endpoint** and an authentication **key** for your Azure AI services resource. Save your changes.
4. Note that the **Lab02** folder contains a code file for the client application:

    
    - **Python**: image-analysis.py

    Open the code file and at the top, under the existing namespace references, find the comment **Import namespaces**. Then, under this comment, add the following language-specific code to import the namespaces you will need to use the Azure AI Vision SDK:

    **Python**
    
    ```Python
    # import namespaces
    from azure.ai.vision.imageanalysis import ImageAnalysisClient
    from azure.ai.vision.imageanalysis.models import VisualFeatures
    from azure.core.credentials import AzureKeyCredential
    ```
    
## View the images you will analyze

In this exercise, you will use the Azure AI Vision service to analyze multiple images.

1. In Github Codespace, expand the **lab02** folder and the **images** folder it contains.
2. Select each of the image files in turn to view them in Github Codespace.

## Analyze an image to suggest a caption

Now you're ready to use the SDK to call the Vision service and analyze an image.

1. In the code file for your client application, in the **Main** function, note that the code to load the configuration settings has been provided. Then find the comment **Authenticate Azure AI Vision client**. Then, under this comment, add the following language-specific code to create and authenticate a Azure AI Vision client object:

**Python**

```Python
# Authenticate Azure AI Vision client
cv_client = ImageAnalysisClient(
    endpoint=ai_endpoint,
    credential=AzureKeyCredential(ai_key)
)
```

2. In the **Main** function, under the code you just added, note that the code specifies the path to an image file and then passes the image path to two other functions (**AnalyzeImage** and **BackgroundForeground**). These functions are not yet fully implemented.

3. In the **AnalyzeImage** function, under the comment **Get result with specify features to be retrieved**, add the following code:

**Python**

```Python
# Get result with specified features to be retrieved
result = cv_client.analyze(
    image_data=image_data,
    visual_features=[
        VisualFeatures.CAPTION,
        VisualFeatures.DENSE_CAPTIONS,
        VisualFeatures.TAGS,
        VisualFeatures.OBJECTS,
        VisualFeatures.PEOPLE],
)
```
    
4. In the **AnalyzeImage** function, under the comment **Display analysis results**, add the following code (including the comments indicating where you will add more code later.):



**Python**

```Python
# Display analysis results
# Get image captions
if result.caption is not None:
    print("\nCaption:")
    print(" Caption: '{}' (confidence: {:.2f}%)".format(result.caption.text, result.caption.confidence * 100))

# Get image dense captions
if result.dense_captions is not None:
    print("\nDense Captions:")
    for caption in result.dense_captions.list:
        print(" Caption: '{}' (confidence: {:.2f}%)".format(caption.text, caption.confidence * 100))

# Get image tags


# Get objects in the image


# Get people in the image

```
    
5. Save your changes and return to the integrated terminal for the **lab02** folder, and enter the following command to run the program with the argument **images/street.jpg**:


**Python**

```
python image-analysis.py images/street.jpg
```
    
6. Observe the output, which should include a suggested caption for the **street.jpg** image.
7. Run the program again, this time with the argument **images/building.jpg** to see the caption that gets generated for the **building.jpg** image.
8. Repeat the previous step to generate a caption for the **images/person.jpg** file.

## Get suggested tags for an image

It can sometimes be useful to identify relevant *tags* that provide clues about the contents of an image.

1. In the **AnalyzeImage** function, under the comment **Get image tags**, add the following code:

**Python**

```Python
# Get image tags
if result.tags is not None:
    print("\nTags:")
    for tag in result.tags.list:
        print(" Tag: '{}' (confidence: {:.2f}%)".format(tag.name, tag.confidence * 100))
```

2. Save your changes and run the program once for each of the image files in the **images** folder, observing that in addition to the image caption, a list of suggested tags is displayed.

## Detect and locate objects in an image

*Object detection* is a specific form of computer vision in which individual objects within an image are identified and their location indicated by a bounding box..

1. In the **AnalyzeImage** function, under the comment **Get objects in the image**, add the following code:

**Python**

```Python
# Get objects in the image
if result.objects is not None:
    print("\nObjects in image:")

    # Prepare image for drawing
    image = Image.open(image_filename)
    fig = plt.figure(figsize=(image.width/100, image.height/100))
    plt.axis('off')
    draw = ImageDraw.Draw(image)
    color = 'cyan'

    for detected_object in result.objects.list:
        # Print object name
        print(" {} (confidence: {:.2f}%)".format(detected_object.tags[0].name, detected_object.tags[0].confidence * 100))
        
        # Draw object bounding box
        r = detected_object.bounding_box
        bounding_box = ((r.x, r.y), (r.x + r.width, r.y + r.height)) 
        draw.rectangle(bounding_box, outline=color, width=3)
        plt.annotate(detected_object.tags[0].name,(r.x, r.y), backgroundcolor=color)

    # Save annotated image
    plt.imshow(image)
    plt.tight_layout(pad=0)
    outputfile = 'objects.jpg'
    fig.savefig(outputfile)
    print('  Results saved in', outputfile)
```

2. Save your changes and run the program once for each of the image files in the **images** folder, observing any objects that are detected. After each run, view the **objects.jpg** file that is generated in the same folder as your code file to see the annotated objects.

## Detect and locate people in an image

*People detection* is a specific form of computer vision in which individual people within an image are identified and their location indicated by a bounding box.

1. In the **AnalyzeImage** function, under the comment **Get people in the image**, add the following code:

**Python**

```Python
# Get people in the image
if result.people is not None:
    print("\nPeople in image:")

    # Prepare image for drawing
    image = Image.open(image_filename)
    fig = plt.figure(figsize=(image.width/100, image.height/100))
    plt.axis('off')
    draw = ImageDraw.Draw(image)
    color = 'cyan'

    for detected_people in result.people.list:
        if detected_people.confidence > 0.8:
            # Draw object bounding box
            r = detected_people.bounding_box
            bounding_box = ((r.x, r.y), (r.x + r.width, r.y + r.height))
            draw.rectangle(bounding_box, outline=color, width=3)

        # Return the confidence of the person detected
        print(" {} (confidence: {:.2f}%)".format(detected_people.bounding_box, detected_people.confidence * 100))
        
    # Save annotated image
    plt.imshow(image)
    plt.tight_layout(pad=0)
    outputfile = 'people.jpg'
    fig.savefig(outputfile)
    print('  Results saved in', outputfile)
```


3. Save your changes and run the program once for each of the image files in the **images** folder, observing any objects that are detected. After each run, view the **people.jpg** file that is generated in the same folder as your code file to see the annotated objects.


## Remove the background or generate a foreground matte of an image

In some cases, you may need to create remove the background of an image or might want to create a foreground matte of that image. Let's start with the background removal.

1. In your code file, find the **BackgroundForeground** function; and under the comment **Remove the background from the image or generate a foreground matte**, add the following code:


**Python**

```Python
# Remove the background from the image or generate a foreground matte
print('\nRemoving background from image...')
    
url = "{}computervision/imageanalysis:segment?api-version={}&mode={}".format(endpoint, api_version, mode)

headers= {
    "Ocp-Apim-Subscription-Key": key, 
    "Content-Type": "application/json" 
}

image_url="https://github.com/MicrosoftLearning/mslearn-ai-vision/blob/main/Labfiles/01-analyze-images/Python/image-analysis/{}?raw=true".format(image_file)  

body = {
    "url": image_url,
}
    
response = requests.post(url, headers=headers, json=body)

image=response.content
with open("background.png", "wb") as file:
    file.write(image)
print('  Results saved in background.png \n')
```
    
2. Save your changes and run the program once for each of the image files in the **images** folder, opening the **background.png** file that is generated in the same folder as your code file for each image.  Notice how the background has been removed from each of the images.

Let's now generate a foreground matte for our images.

4. Save your changes and run the program once for each of the image files in the **images** folder, opening the **background.png** file that is generated in the same folder as your code file for each image.  Notice how a foreground matte has been generated for your images.

## ACTION Lab 02

**Upload the following content to ADI for Lab 02** 
- completed **image-analysis.py** app
- **people.jpg** as the result of running analysis for **building.jpg**
- **objects.jpg** as the result of running analysis for **street.jpg**
- **Background.jpg** as the result of running analysis for **person.jpg**

## More information

In this exercise, you explored some of the image analysis and manipulation capabilities of the Azure AI Vision service. The service also includes capabilities for detecting objects and people, and other computer vision tasks.

For more information about using the **Azure AI Vision** service, see the [Azure AI Vision documentation](https://learn.microsoft.com/azure/ai-services/computer-vision/).