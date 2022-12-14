<template>
  <div class="popper-wrapper">
    <div class="popper-popup" :class="{ hidden: !popupState }">
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref, computed } from 'vue'
import data from '@/assets/data.json'
const _elements = ref([]);
const _elementsData = ref([]);
const popupState = ref(false);
let popperData;
const hidePopup = () => {
  popupState.value = false;
}
onMounted(() => {
  popperData = JSON.parse(window.popper_data);
  // popperData = data;
  setTimeout(() => {
    popupState.value = true;
    renderElements(popperData)

  }, 1000);
})
const renderElements = (data) => {
  data.forEach((item, key) => {
    _elementsData.value.push(item);
    const el = document.createElement('div')
    el.id = `pi-${key}`

    el.className = 'popper-item'
    el.dataset.type = item.type;
    document.querySelector('.popper-popup').appendChild(el)
    _elements.value.push(el);
    if (item.type == 'button') {
      item.objects.forEach((child, childKey) => {
        _elementsData.value.push(child);
        const child_el = document.createElement('div')
        child_el.id = `pi-${key}-${childKey}`
        child_el.className = 'popper-item'
        child_el.dataset.type = child.type;
        _elements.value.push(child_el);
        el.append(child_el)
      })
    }
  })
  renderStyles();
  createCloseButton();
}
const renderStyles = () => {
  _elementsData.value.map((data, key) => {
    let dom_el = _elements.value[key];
    applyStyles(dom_el, data)
  })
}

const applyStyles = (el, data) => {
  let hasParent = el.parentElement.dataset.type == 'button';
  //Replace Texts
  if (data.type == 'textbox' && !hasParent) {
    let text = createText(data);
    let parent = el.parentElement;
    parent.removeChild(el);
    parent.append(text);
  }
  //Replace image
  if (data.type == 'image') {
    let image = createImage(data);
    let parent = el.parentElement;
    parent.removeChild(el);
    parent.append(image);
  }

  //Replace input 
  if (data.type == 'input') {
    let btn = createInput(data);
    let parent = el.parentElement;
    parent.removeChild(el);
    parent.append(btn);
  }
  //Replace Button 
  if (data.type == 'button') {
    let btn = createButton(data);
    let parent = el.parentElement;
    parent.removeChild(el);
    parent.append(btn);
  }

  //soft styles

  el.style.borderRadius = `${data.radius}px`;

  el.style.border = `${data.strokeWidth}px solid ${data.stroke}`

  el.style.backgroundColor = data.backgroundColor;
  el.style.background = data.fill;


  if (data.type == 'circle') {
    el.style.borderRadius = `50%`;
  }

  el.style.fontSize = `${data.fontSize}px`
  el.style.fontFamily = data.fontFamily
  el.style.color = data.fill

  //dataset
  el.dataset.type = data.type;


  //position styles
  el.style.top = `${data.top}px`
  el.style.left = `${data.left}px`
  el.style.width = `${data.width}px`
  el.style.height = `${data.height}px`
  el.style.position = 'absolute'







}
const createText = (data) => {
  let text = document.createElement('div');

  text.innerHTML = data.text;
  text.backgroundColor = "";
  text.background = "";
  text.textAlign = data.textAlign;

  text.className = 'popper-text'
  text.innerHTML = data.text;
  text.style.color = data.fill
  text.style.lineHeight = `1.3em`
  text.style.fontFamily = data.fontFamily
  text.style.fontSize = `${data.fontSize}px`
  text.style.textAlign = data.textAlign
  text.style.position = "absolute"
  text.style.top = `${data.top}px`
  text.style.left = `calc(${data.left}px)`
  text.style.width = `${data.width}px`
  text.style.height = `${data.height}px`
  text.style.transform = `scale(${data.scaleX}, ${data.scaleY})`


  return text;
}
const createButton = (data) => {
  let btn = document.createElement('button');

  btn.className = 'popper-btn'
  btn.innerHTML = data.objects[1].text;

  btn.style.background = data.objects[0].fill
  btn.style.border = "none";
  btn.style.borderRadius = `${data.objects[0].rx}px`;
  btn.style.color = data.objects[1].fill
  btn.style.fontFamily = data.objects[1].fontFamily
  btn.style.fontSize = `${data.objects[1].fontSize}px`
  btn.style.fontWeight = 'bolder'

  btn.style.width = `${data.objects[0].width}px`
  btn.style.height = `${data.objects[0].height}px`

  btn.style.position = "absolute"
  btn.style.top = `calc(${data.top}px - 20px)`
  btn.style.left = `calc(${data.left}px - 80px)`
  btn.style.right = `${data.right}px`
  btn.style.bottom = `${data.bottom}px`
  btn.style.transform = `scale(${data.objects[0].scaleX}, ${data.objects[0].scaleY})`
  return btn;
}
const createInput = (data) => {
  let input = document.createElement('input');

  input.className = 'popper-input'
  input.placeholder = data.objects[1].text;
  input.innerHTML = data.objects[1].text;

  input.style.background = data.objects[0].fill
  input.style.border = "none";
  input.style.borderRadius = `${data.objects[0].rx}px`;
  input.style.color = data.objects[1].fill
  input.style.fontFamily = data.objects[1].fontFamily
  input.style.fontSize = `${data.objects[1].fontSize}px`

  input.style.width = `calc(${data.objects[0].width}px - 10px)`
  input.style.height = `${data.objects[0].height}px`

  input.style.position = "absolute"
  input.style.top = `calc(${data.top}px - 25px)`
  input.style.left = `calc(${data.left}px - 165px)`
  input.style.right = `${data.right}px`
  input.style.bottom = `${data.bottom}px`
  input.style.transform = `scale(${data.scaleX}, ${data.scaleY})`
  return input;
}
const createImage = (data) => {
  let img = document.createElement('img');
  img.src = data.src;
  img.className = 'popper-img'
  img.style.width = `${data.width}px`
  img.style.height = `${data.height}px`
  img.style.position = "absolute"
  img.style.top = `calc(${data.top}px - 50px)`
  img.style.left = `calc(${data.left}px - 140px)`
  img.style.right = `${data.right}px`
  img.style.bottom = `${data.bottom}px`

  img.style.transform = `scale(${data.scaleX}, ${data.scaleY}) rotate(${data.angle}deg)`
  return img;
}
const createCloseButton = () => {
  let close = document.createElement('button');
  close.className = 'popper-close'
  close.innerHTML = 'X';
  close.style.position = "absolute"
  close.style.top = `50px`
  close.style.left = `0px`
  close.style.right = `0px`
  close.style.bottom = `0px`
  close.style.transform = `scale(1, 1)`
  close.style.background = "black"
  close.style.color = "white"
  close.style.width = "50px"
  close.style.height = "50px"
  close.style.borderRadius = "50%"
  close.style.fontSize = "20px"
  close.style.cursor = "pointer"
  close.onclick = () => {
    hidePopup();
  }
  document.getElementsByClassName('popper-item')[0].appendChild(close);
}
</script>

<style>
.popper-wrapper {
  position: absolute;
  display: flex;
  justify-content: center;
  align-items: center;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.05);
}

.popper-popup.hidden {
  scale: 0 !important;
}

.popper-popup {
  position: relative;
  width: 800px;
  height: 800px;
  scale: 1;
  transition: .2s all ease;
}

.popper-btn {
  cursor: pointer;
  opacity: .9;
  transition: .3s all ease;
}

.popper-input {
  padding-left: 20px;
  margin-right: -10px;
}

.popper-btn:hover {
  opacity: 1;
}

.popper-close {
  transition: 1s all ease;
}

/* media queries */
@media (max-width: 800px) {
  .popper-popup {
    scale: 0.9 !important;
    position: fixed;
    margin: 0 auto;
  }

  .popper-wrapper {
    width: 100%;
    width: 100%;
  }
}

@media (max-width: 600px) {
  .popper-popup {
    scale: 0.8 !important;
    position: fixed;
    margin: 0 auto;
  }

  .popper-wrapper {
    width: 100%;
    width: 100%;
  }
}

@media (max-width: 400px) {
  .popper-popup {
    scale: 0.6 !important;
    position: fixed;
    margin: 0 auto;
  }

  .popper-wrapper {
    width: 100%;
    width: 100%;
  }
}
</style>
