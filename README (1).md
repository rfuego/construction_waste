# Модель  для автоматического распознавания видов отходов строительства и сноса с использованием React with Fast API

## 1. О чем этот проект?

Команда Fuego представляет модель и интерфейс для автоматического распознавания видов отходов строительства и сноса. В качестве решения использована модель YOLOv8, обученная на датасете, собранном в сервисе Roboflow, и дополнительно дообученная на данных, которые получены с камер видеонаблюдения.

Разработанное решение позволяет классифицировать отходы строительного мусора и сноса в кузове спецтехники, и предотвратить подмену заявленного типа, который указан при запуске машины в рейс.

## 2. Почему он необходим?

В настоящее время для Департамента, как и для города в целом, одной из актуальных проблем обращения с отходами строительства является их подмена и несанкционированный сброс в непредназначенных для этого местах недобросовестными перевозчиками. Определение вида отходов в кузове самосвалов с помощью анализа видеопотока с камер видеонаблюдения и других объективных средств контроля позволит обеспечить своевременное выявление вида перевозимых отходов строительства и значительно сократить случаи подлогов, оплаты некорректных перевозок и нелегальных сбросов отходов в городе. 

Необходимо реализовать программный модуль на основе искусственного интеллекта, который будет в состоянии проводить анализ содержимого кузова самосвала, перевозящего отходы строительства и сноса, и автоматически определять какой тип отходов он перевозит.

## 3. Концепт

Прежде чем углубиться в проект, я дам вам обзор основных концепций и технологий, которые были использованы, а также ссылки на ресурсы, где вы можете узнать об этом больше.

### 3.1 Стэк технологий

- **YOLOv8**
- **Pytorch**
- **React**
- **OpenCV**
- **Python**
- **FastAPI**
### 3.2 React как "интерфейсный" интерфейс 

Возможно, вы уже слышали о React, поэтому можете пропустить этот раздел. 

[React](https://reactjs.org/) - это библиотека JavaScript для создания пользовательского интерфейса. Она была создана Facebook (она же Meta) примерно в 2013 году. 

Насколько я смог узнать во время работы над этим проектом, React работает декларативно, и одна из особенностей, которая делает его классным, заключается в том, что вы можете удалять компоненты, которые имеют свое собственное состояние, и позже могут быть повторно использованы в приложении, которое вы создаете. 

Исходя из чисто серверного опыта, я могу сказать, что было не так уж сложно получить и включить его в то, чего я хотел достичь, а именно создать простой пользовательский интерфейс, который позволит мне писать текст и ждать, пока сервер сгенерирует изображение.

#### 3.2.1 Resources
- [React Docs](https://reactjs.org/docs/getting-started.html)

### 3.3 FastAPI as backend

Здесь становится интереснее, когда появляется серверная часть, и я обнаружил все, что вы можете делать, работая с FastAPI. 

Как указано на их веб-сайте: 
> > Fast API - это современный, быстрый (высокопроизводительный) веб-фреймворк для создания API с Python 3.7+ на основе стандартных подсказок типа Python.

Что мне нравится в Fast API, так это возможность быстро создавать API без особых хлопот. Мне понравился тот факт, что я мог определять маршруты и сразу же проверять их, просматривая документы API, предоставленные Swagger. 

В дополнение к этому, вы могли бы определить свою модель данных как класс, используя педантичное наследование от базовой модели.

#### 3.3.1 Resources
- [FastAPI Docs](https://fastapi.tiangolo.com/)
- [FastAPI Tutorial - User Guide](https://fastapi.tiangolo.com/tutorial/)

## Project Structure

```
├── backend                          ----> Contains all the backend FastAPI work
│   ├── dev-requirements.txt
│   ├── main.py                      ----> Endpoints definition
│   ├── requirements.txt
│   ├── run_uvicorn.py
│   ├── schemas.py                   ----> Define your data models here
│   └── services.py                  ----> Define all the heavy load work to be done here.
|                                          in this case all the HuggingFace setup and work for generating the 
|                                          stable diffusion image
└── frontend
    ├── README.md
    ├── package-lock.json
    ├── package.json
    ├── public
    │   ├── index.html
    │   ├── manifest.json
    │   └── robots.txt
    └── src
        ├── App.jsx                 ---> Main App definition where you will embed your components as well
        ├── components
        │   ├── ErrorMessage.jsx
        │   ├── GenImage.jsx        ---> Definition of the UI components as well as the call to the backend 
                                         using fetch API
        │   └── Header.jsx          ---> Minimal Header definition
        └── index.js
```

## Setup and run it :) 

These are the steps to see it running :) 

### From your backend folder: 

1. You need a HugginFace token. Checkout how to create one [here](https://huggingface.co/docs/hub/security-tokens#how-to-manage-user-access-tokens)
2. Once you have your token created it follow the next steps

```
cd backend 
touch .env 
```

3. Open the .env file and add your token there like this: 

```
HF_TOKEN=MY_FANCY_TOKEN
```

**WARNING**: Make sure you never commit your keys or tokens file :) Add this file to your .gitignore

4. Create your environment and activate your environment
```
python -m venv venv 
source venv/bin/activate
pip install -r requirements.txt
```

5. Startup your backend 

```
uvicorn main:app --port 8885
```

### From your frontend folder

```
cd frontend
npm install 
npm start
```

## How to Generate an Image? 

Fill the parameters as follows:

- Prompt: Text to express the wish for your image. 

    `Example: A red racing car winning the formula-1` 
- Seed: a number indicating the seed so that your output will be deterministic. 
- Guidance scale: it's a float that basically try to enforce that the generation of the image better match the prompt. 

    > Side note: if you are curious about this parameter, you can do a deep dive by reading the paper [Classifier-free Diffusion Guidance](https://arxiv.org/pdf/2207.12598.pdf)
- Number of Inference Steps: It's a number usually between 50 and 100. This number indicates de amount of denoising steps. Keep in mind the higher the number the more time the inference ( image generation ) will take. 

You should be able to see your frontend like this one below: 

<p align="center">
<img src="./assets/demo.png" alt="Demo example" width=700 height=400 />
</p>

## Resources I've used to build the project

- Webinar: [FastAPI for Machine Learning: Live coding an ML web application](https://www.youtube.com/watch?v=_BZGtifh_gw)
- FastAPI and ReactJS, a set of videos by [Rithmic - Youtube Channel](https://youtube.com/playlist?list=PLhH3UpV2flrwfJ2aSwn8MkCKz9VzO-1P4)
- [Stable Diffusion with 🧨 Diffusers by HuggingFace](https://huggingface.co/blog/stable_diffusion)

## ToDo

- Add endpoint that shows a grid of images instead of one
- Update the service backend with a new function to return multiple images. 
- Improve the UI by customizing Bulma with a different style. 
