![Header image](headerImage.png)
# 🚀 iOS Developer Roadmap 
Roadmap to becoming an iOS developer in 2018.

## ☝️ What is it?
This is a tree-like compilation of topics highly relevant to iOS development. Both image and text version are generated from human readable content file.

## ✌️ Who is it for?
The roadmap will be helpul for:

- anyone who wants to become an iOS developer
- iOS developers who desire to become experts
- iOS developers who are preparing for interviews and need to brush up knowledge
- iOS developers who need to compile own set of interview questions


## 👌 Why these topics?
See [this article](https://medium.com/@borlov/c9a24f413457) explaining selection of topics.

## 👨‍🎓 How to use this roadmap
1. Find a topic you want to study.
2. Go to corresponding resources section.
3. Study until you can confidently explain the topic to your cat.
4. (Optional) tick the checkbox next to the topic. [How to tick a checkbox](HowTo/HOWTOCHECKBOX.md).
4. Go to step 1.

`Essential topics` are topics which significantly contribute to understanding of iOS development. Consequently, it is a good idea to study them first as they are often encountered on interviews.

Start from `Getting started` section if you haven't done any iOS development yet.

## 🗺 Image version 
Roadmap of essential topics. Roadmap for all topics is [here.](RoadmapProject/Script/Generated/ROADMAP.png)
![Header image](RoadmapProject/Script/Generated/ESSENTIALROADMAP.png)

## 📝 Text version
[Text version with materials to study.](RoadmapProject/Script/Generated/ROADMAP.md)

## 🤝 How to contribute

- add new topics to `Content.yml`
- add missing study materials to `Content.yml`
- throw ideas at me on [![Twitter: @Bohdan_Orlov](https://img.shields.io/badge/twitter-@Bohdan_Orlov-4d66b3.svg?style=flat)](https://twitter.com/bohdan_orlov)


[The complete contribution guide.](HowTo/HOWTOPR.md)


## ☑️ TODO
- [x] content file with topics and materials
- [x] ability to generate README.md from the content file
- [x] ability to generate Roadmap tree image from the content file
- [ ] make generation script less miserable:
	- [x] make it readable 🤦
	- [ ] output Yaml format violation errors
	- [ ] handle errors of parsing Yaml into Topics and Resources
	- [ ] handle file read/write errors
	- [ ] handle image generation errors
- [x] automatic regeneration of roadmap after every commit
- [ ] automatic validation of content format on PR
- [ ] make sure Travis doesn't deploy if generation script fails

## ⚙️ Generation status
[![Travis](https://travis-ci.org/BohdanOrlov/iOS-Developer-Roadmap.svg?branch=master)](https://travis-ci.org/BohdanOrlov/iOS-Developer-Roadmap)

## 📃 License

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

## 📊 Skills Matrix 
You might also like the [iOS Developer Skills Matrix](https://github.com/BohdanOrlov/ios-skills-matrix).
![Skills matrix](https://github.com/BohdanOrlov/ios-skills-matrix/raw/master/matrix.png)

## 📚 iOS and Swift Tutorials and Courses

Learn iOS development & Swift online from the best iOS Swift tutorials and courses recommended by the programming community. 
https://hackr.io/tutorials/learn-ios-swift


```swift 
//
//  ViewController.swift
//  testUnsplash
//
//  Created by Schultz, Tyler on 3/4/19.
//  Copyright © 2019 Schultz, Tyler. All rights reserved.
//

import UIKit
class ViewController: UIViewController {

    @IBOutlet weak var datePicker: UIDatePicker!
    @IBOutlet weak var descriptionField: UITextField!
    @IBOutlet weak var amountField: UITextField!
    @IBOutlet weak var categoryField: UITextField!
    @IBOutlet weak var notesField: UITextField!
    
    var alert: UIAlertController?
    
    var date: Date = Date()
    var recordDescription: String = ""
    var amount: Float = 0.0
    var category: String = ""
    var notes: String = ""
    @IBOutlet weak var addButton: UIButton!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        self.descriptionField.delegate = self
        self.amountField.delegate = self
        self.categoryField.delegate = self
        self.notesField.delegate = self
        
        self.datePicker.backgroundColor = UIColor.white
        self.datePicker.layer.masksToBounds = true
        self.datePicker.layer.borderWidth = 0.5
        self.datePicker.layer.borderColor = UIColor.lightGray.cgColor
        self.datePicker.layer.cornerRadius = 8.0
        self.addButton.layer.cornerRadius = 8.0
        // Do any additional setup after loading the view, typically from a nib.
    }
    
    func addRecord() {
        showAlert()
        getData()
        let group: Group = Group(fields: Field(postDate: date.toAirtableFormat(),
                                               description: recordDescription,
                                               amount: amount,
                                               categoryType: category,
                                               notes: notes))
        let jsonData = try! JSONEncoder().encode(group)
        self.postRecord(jsonData: jsonData)
    }
    
    @IBAction func addRecordTapped(_ sender: UIButton) {
        self.descriptionField.becomeFirstResponder()
        self.addRecord()
    }
    
    func getData() {
        guard let descriptionText = self.descriptionField.text,
            let amountText = self.amountField.text,
            let categoryText = self.categoryField.text,
            let notesText = self.notesField.text else {
                return
        }
        self.date = self.datePicker.date
        self.recordDescription = descriptionText
        self.amount = Float(amountText) ?? 0.0
        self.category = categoryText
        self.notes = notesText
    }
    
    func clearData() {
        self.datePicker.date = Date()
        self.descriptionField.text = ""
        self.amountField.text = ""
        self.categoryField.text = ""
        self.notesField.text = ""
    }
}

extension ViewController {
    func postRecord(jsonData: Data) {
        let url = URL(string: "https://api.airtable.com/v0/appxbUwr5bTOYrk3W/February")
        var request = URLRequest(url: url!)
        request.addValue("application/json", forHTTPHeaderField: "Content-Type")
        request.addValue("Bearer [KEY]", forHTTPHeaderField: "Authorization")
        request.httpMethod = "POST"
        request.httpBody = jsonData
        let task = URLSession.shared.dataTask(with: request) { data, response, error in
            guard let data = data,
                let response = response as? HTTPURLResponse,
                error == nil else {                                              // check for fundamental networking error
                    print("error", error ?? "Unknown error")
                    DispatchQueue.main.async {
                        self.showFailure()
                    }
                    return
            }
            
            guard (200 ... 299) ~= response.statusCode else {                    // check for http errors
                print("statusCode should be 2xx, but is \(response.statusCode)")
                print("response = \(response)")
                DispatchQueue.main.async {
                    self.showFailure()
                }
                return
            }
            
            let responseString = String(data: data, encoding: .utf8)
            DispatchQueue.main.async {
                self.clearData()
                self.showComplete()
            }
            print("responseString = \(responseString)")
        }
        task.resume()
    }
}

extension ViewController {
    func handleAlert(title: String, message: String) {
        guard alert == nil else {
            self.resetAlert(title: title, message: message)
            return
        }
        alert = UIAlertController(title: title, message: message, preferredStyle: UIAlertController.Style.alert)
        if let alertController = alert {
            self.present(alertController, animated: true, completion: nil)
        }
    }
    
    func showAlert() {
        handleAlert(title: "🔄 Uploading", message: "Please Wait! \n\n")
    }
    
    func showComplete() {
        handleAlert(title: "✅ Complete", message: "Add another Record! \n\n")
    }
    
    func showFailure() {
        handleAlert(title: "❌ Failure", message: "Please try again! \n\n")
    }
    
    func resetAlert(title: String, message: String) {
        alert?.dismiss(animated: false, completion: {
            self.alert = UIAlertController(title: title, message: message, preferredStyle: UIAlertController.Style.alert)
            self.alert?.addAction(UIAlertAction(title: "Okay", style: .default, handler: nil))
            if let alertController = self.alert {
                self.present(alertController, animated: true, completion: nil)
            }
            self.alert = nil
        })
    }
}

extension ViewController: UITextFieldDelegate {
    func textFieldShouldReturn(_ textField: UITextField) -> Bool {
        textField.resignFirstResponder()
        let tag = textField.tag
        switch tag {
        case 1:
            self.amountField.becomeFirstResponder()
        case 2:
            self.categoryField.becomeFirstResponder()
        case 3:
            self.notesField.becomeFirstResponder()
        case 4:
            self.addRecord()
        default:
            break
        }
        return false
    }
}

struct Group: Codable {
    let fields: Field
}

struct Field: Codable {
    let postDate: String
    let description: String
    let amount: Float
    let categoryType: String
    let notes: String
    
    enum CodingKeys: String, CodingKey {
        case postDate = "Post Date"
        case description = "Description"
        case amount = "Amount"
        case categoryType = "Category Type"
        case notes = "Notes"
    }
}

extension Date {
    func toAirtableFormat() -> String {
        let dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "yyyy-MM-dd"
        return dateFormatter.string(from: self)
    }
}


```
