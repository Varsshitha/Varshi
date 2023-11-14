# Varshi
MODEL
//
//  DataFile.swift
//  Exam02
//
//  Created by Maddi,Himaja Reddy on 11/8/22.
//

import Foundation

struct Student{
    var fullName : String
    var transferCredits : Double
    var startYear : Int
    var startMonth : Int
    
    static func loadGradData() -> [Student]{
        var student = [Student]()
        
        student.append(Student(fullName: "Michael P. Smith", transferCredits: 5, startYear: 2019, startMonth: 01))
        student.append(Student(fullName: "Sean N. Noah", transferCredits: 12, startYear: 2020, startMonth: 08))
        student.append(Student(fullName: "John H. Carter", transferCredits: 0, startYear: 2013, startMonth: 08))
        return student
    }
    
    static func loadUnderGradData() ->[Student]{
        var student = [Student]()
        
        student.append(Student(fullName: "Emma P. Lee", transferCredits: 3, startYear: 2018, startMonth: 08))
        student.append(Student(fullName: "Charlotte J. Swann", transferCredits: 6, startYear: 2019, startMonth: 08))
        student.append(Student(fullName: "Lisa K. Rock", transferCredits: 6, startYear: 2022, startMonth: 01))
        student.append(Student(fullName: "Mary G. Paula", transferCredits: 0, startYear: 2013, startMonth: 08))
        return student
    }
}

struct UtilityConstants{
    static let underGradMin = 12.0
    static let underGradMax = 18.0
    static let underGradTotal = 120.0
    static let gradMin = 9.0
    static let gradMax = 12.0
    static let gradTotal = 33.0
}

Student TVC
import UIKit

class StudentsTVC: UIViewController, UITableViewDelegate, UITableViewDataSource {
    
    @IBOutlet weak var studentsTV: UITableView!
    
    var underGradStudents = Student.loadUnderGradData()
    var gradStudents = Student.loadGradData()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.navigationItem.title = "Students"
        
        self.studentsTV.delegate = self
        self.studentsTV.dataSource = self
    }
    
    func numberOfSections(in tableView: UITableView) -> Int {
        2
    }
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        switch section{
        case 0:
            return underGradStudents.count
        default:
            return gradStudents.count
        }
    }
    
    func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        switch section{
        case 0:
            return "Under Graduate"
        default:
            return "Graduate"
        }
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        switch indexPath.section{
        case 0 :
            let row = tableView.dequeueReusableCell(withIdentifier: "ugCell", for: indexPath)
            let student = underGradStudents[indexPath.row]
            row.textLabel?.text = student.fullName
            row.detailTextLabel?.text = "Started: \(student.startMonth)/\(student.startYear)     Transfer Credits: \(student.transferCredits)"
            return row
        case 1 :
            let row = tableView.dequeueReusableCell(withIdentifier: "gradCell", for: indexPath)
            let student = gradStudents[indexPath.row]
            row.textLabel?.text = student.fullName
            row.detailTextLabel?.text = "Started: \(student.startMonth)/\(student.startYear)     Transfer Credits: \(student.transferCredits)"
            return row
        default :
            break
        }
        return UITableViewCell()
    }
    
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        self.performSegue(withIdentifier: "courseSegue", sender: indexPath)
    }
    
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        let segueName = segue.identifier
        
        switch segueName {
        case "courseSegue" :
            if  let dest = segue.destination as? ProfileVC{
                let idxPath = sender as! IndexPath
                if idxPath.section == 0{
                    dest.student = underGradStudents[idxPath.row]
                    dest.isUndergrad = true
                }else{
                    dest.student = gradStudents[idxPath.row]
                    dest.isUndergrad = false
                }
            }
        default:
            break
        }
    }
}
profile VC
import UIKit

class ProfileVC: UIViewController {
    
    @IBOutlet weak var profileIV: UIImageView!
    @IBOutlet weak var nameLBL: UILabel!
    @IBOutlet weak var creditHrsLBL: UILabel!
    @IBOutlet weak var earliestGradLBL: UILabel!    
    @IBOutlet weak var latestGradLBL: UILabel!
    
    var student: Student?
    
    var isUndergrad = true
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.navigationItem.title = "Student Dashboard"
        
        nameLBL.text = self.student?.fullName
        creditHrsLBL.text = "\(self.student!.transferCredits)"
        anticipatedGraduation()
    }
    
    func anticipatedGraduation(){
        
        if isUndergrad{
            let diff = UtilityConstants.underGradTotal - self.student!.transferCredits
            let earliest = ceil(diff / UtilityConstants.underGradMax)
            self.earliestGradLBL.text = "\(earliest) semesters exluding summer semesters."
            let latest = ceil(diff / UtilityConstants.underGradMin)
            self.latestGradLBL.text = "\(latest) semesters exluding summer semesters."
        }else{
            let diff = UtilityConstants.gradTotal - self.student!.transferCredits
            let earliest = ceil(diff / UtilityConstants.gradMax)
            self.earliestGradLBL.text = "\(earliest) semesters exluding summer semesters."
            let latest = ceil(diff / UtilityConstants.gradMin)
            self.latestGradLBL.text = "\(latest) semesters exluding summer semesters."
        }
    }
}




