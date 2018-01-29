# सुरक्षा, मूल क्षमतायें, और आपकी जिम्मेदारी

वेब डेवलपर्स के रूप में हम अक्सर ब्राउज़र के सुरक्षा कवच से सुरक्षित महसूस करते हैं - कोडिंग से जुड़े जोखिम अपेक्षाकृत कम होते हैं | हमारी वेबसाइट्स को एक सैंडबॉक्स में सीमित शक्तियाँ ही मिलती हैं, और हमें विश्वास है कि हमारे उपयोगकर्ता एक ऐसा ब्राउज़र इस्तेमाल करते हैं जिसे इंजिनियर्स की एक बड़ी टीम ने बनाया है और जो नये सुरक्षा खतरों के पता चलने पर त्वरित प्रतिक्रिया देते हैं |

इलेक्ट्रॉन इस्तेमाल करते समय, यह समझना बेहद ज़रूरी है कि यह एक वेब ब्राउज़र नहीं है | यह आपको परिचित वेब तकनीकों का इस्तेमाल कर सुविधा-संपन्न डेस्कटॉप एप्लीकेशनस बनाने की क्षमता प्रदान करता है, पर आपके कोड के पास कहीं ज्यादा शक्ति होती है | जावास्क्रिप्ट फाइलसिस्टम, यूजर शैल और दूसरी बहुत सी चीजों तक पहुँच सकता है | इससे आप उच्च गुणवत्ता की मूल एप्लीकेशनस बना सकते हैं, पर आपके कोड को मिली अधिक शक्तियों से सुरक्षा जोकिम भी अधिक हो जाते हैं |

इस बात को ध्यान में रखते हुए, आपके के लिये यह जानना ज़रूरी है कि अविश्वश्नीय स्त्रोतों से मनमानी सामग्री प्रदर्शित करने पर एक गंभीर सुरक्षा जोखिम उत्पन्न हो सकता है, जिसे संभालने के लिए इलेक्ट्रॉन हस्तक्षेप नहीं कर सकता | असल में, बहुत सी लोकप्रिय इलेक्ट्रॉन एप्प्स (एटम, स्लैक, विसुअल स्टूडियो कोड, आदि) मूलतः स्थनीय सामग्री प्रदर्शित करती हैं (या विश्वसनीय, नोड इंटीग्रेशन के बिना सुरक्षित दूरस्थ सामग्री ) - अगर आपकी एप्लीकेशन एक ऑनलाइन स्त्रोत से कोड चलाती है, तो यह यह सुनिश्चित करने की आपकी जिम्मेदारी है कि वह कोड बुरा नहीं है|

## सुरक्षा समस्याओं को रिपोर्ट करना

एक इलेक्ट्रॉन सुरक्षाछिद्र को कैसे रिपोर्ट करें, इसकी जानकारी पाने के लिए [SECURITY.md](https://github.com/electron/electron/tree/master/SECURITY.md) देखें

## क्रोमियम सुरक्षा समस्यायें और अपग्रेडस

हालाँकि इलेक्ट्रॉन की कोशिश क्रोमियम के नये संस्करणों को जल्द से जल्द समर्थित करने की होती है, डेवलपर्स के लिए यह जानना आवश्यक है कि अपग्रेड करना एक बेहद बड़ा काम है - जिसमे कई दर्जन या फिर कई बार सैकड़ों फाइल्स को भी खुद एडिट करना होता पड़ता है | आज के हिसाब से मौज़ूद संसाधनों और योगदानों के हिसाब से, इलेक्ट्रॉन के पास अक्सर क्रोमियम का नवीनतम संस्करण उपलब्ध नहीं होगा, और यह देरी कुछ दिनों या हफ़्तों की भी हो सकती है |

हमें लगता है कि क्रोमियम तत्व को अपडेट करने का हमारा मौजादा सिस्टम हमारे पास उपलब्ध संसाधनों और फ्रेमवर्क पर निर्मित ज्यादातर एप्लीकेशनस की आवश्यकताओं के बीच में एक उचित संतुलन बनाता है | जो लोग इलेक्ट्रॉन के ऊपर एप्लीकेशनस का निर्माण करते हैं, हम उनसे निश्चित रूप से विशिष्ट उपयोग मामलों के बारे में और अधिक जानना चाहेंगे | इस प्रयास का समर्थन करने वाले पुल रेकुएस्ट्स और योगदानों का सर्वदा स्वागत है |

## उपरोक्त सलाह की अनदेखी

जब भी आप एक दूरस्थ स्त्रोत से कोड प्राप्त कर उसे स्थानीय रूप से चलाते है तो एक सुरक्षा समस्या जरूर उत्पन्न होती है | उदाहरण के तौर पर, एक दूरस्थ वेबसाइट लीजिये जो कि एक ब्राउज़र विंडो में प्रदर्शित हो रही है | अगर एक हमलावर किसी तरह से इस सामग्री में बदलाव कर दे (चाहे सीधे स्त्रोत पर हमला कर के, या फिर आपकी एप्प और असल गंतव्य के बीच में बैठ कर), तो वह उपयोगकर्ता की मशीन पर मूल कोड चलाने में सक्षम हो जायेगा |

> :warning: किसी भी परिस्थिति में आपको उस दूरस्थ कोड को लोड और एक्सीक्यूट नहीं करना चहिये जिसमे नोड इंटीग्रेशन इनेबल्ड हो | इसकी जगह, नोड कोड को चलाने के लिए केवल स्थानीय फाइल्स (आपकी एप्लीकेशन संग पैकेज्ड) का इस्तेमाल करें | दूरस्थ सामग्री को प्रदर्शित करने के लिए, `webview` टैग का इस्तेमाल करें और यह सुनिश्चित करें कि `nodeIntegration` डिसेबल्ड है |

#### जाँच सूची

यह पूरी तरह से अभेद्य नहीं है, पर आपको कम से कम इनको तो अपनाना ही चाहिये:

* केवल सुरक्षित (https) सामग्री प्रदर्शित करें
* नोड इंटीग्रेशन को उन सभी रेंदेरेर में डिसएबल कर दें जो दूरस्थ सामग्री प्रदर्शित करते हैं (`webPreferences` में `nodeIntegration` को `false` सेट करना)
* कॉन्टेक्स्ट आइसोलेशन को उन सभी रेंदेरेर में डिसएबल कर दें जो दूरस्थ सामग्री प्रदर्शित करते हैं (`webPreferences` में `contextIsolation` को `true` सेट करना)
* दूरस्थ सामग्री लोड करने वाले सभी सत्रों में `ses.setPermissionRequestHandler()` का उपयोग करें
* `webSecurity` को डिसएबल न करें | उसे डिसएबल करने से सेम-ओरिजिन पॉलिसी भी डिसएबल हो जायेगी |
* एक [`सामग्री-सुरक्षा-नीति`](http://www.html5rocks.com/en/tutorials/security/content-security-policy/) परिभाषित करें, और प्रतिबंधक नियम (यानी कि `script-src 'self'`) इस्तेमाल करें
* [`eval` को ओवरराइड और डिसएबल करें](https://github.com/nylas/N1/blob/0abc5d5defcdb057120d726b271933425b75b415/static/index.js#L6-L8), जो कि स्ट्रिंग्स को कोड की तरह चलने की क्षमता प्रदान करता है |
* `allowRunningInsecureContent` को true सेट न करें |
* `experimentalFeatures` या `experimentalCanvasFeatures` को इनेबल न करें अगर आप नहीं जानते कि इससे क्या हो सकता है |
* `blinkFeatures` इस्तेमाल तब तक न करें जब तक कि आपको पूरी जानकारी न हो |
* WebViews: `nodeintegration` एट्रिब्यूट न जोड़ें |
* WebViews: `disablewebsecurity` न इस्तेमाल करें
* WebViews: `allowpopups` न इस्तेमाल करें
* WebViews: दूरस्थ सीएसएस/जेएस के साथ `insertCSS` या `executeJavaScript` न इस्तेमाल करें |
* WebViews: सभी `<webview>` टैग्स के विकल्प और पैरामीटर्स को जाँचें इससे पहले कि वे `will-attach-webview` इवेंट से जुड़ें:

```js
app.on('web-contents-created', (event, contents) => {
   contents.on('will-attach-webview', (event, webPreferences,
params) => {
     // अगर प्रीलोड स्क्रिप्ट्स इस्तेमाल न हों तो उन्हें हटा दें या यह सत्यापित करें कि उनकी लोकेशन वैध है
     delete webPreferences.preload
     delete webPreferences.preloadURL

     // नोडइंटीग्रेशन डिसएबल करें
    webPreferences.nodeIntegration = false

     // यूआरएल लोड हो रहा है या नहीं, यह सत्यापित करें
     if (!params.src.startsWith('https://yourapp.com/')) {
       event.preventDefault()
     }
   })
 })
```

यह सूचि केवल खतरों को कम से कम करने के लिए, यह उन्हें हटाती नहीं है |अगर आपका लक्ष्य एक वेबसाइट को प्रदर्शित करना है, तो एक ब्राउज़र ज्यादा सुरक्षित विकल्प है |