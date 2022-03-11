---
layout: post
title: How to use UITableViewDiffableDataSource
category: iOS
---

The object you use to manage data and provide cells for a table view.

#### What you need to know

-   [UITableViewDiffableDataSource](https://developer.apple.com/documentation/uikit/uitableviewdiffabledatasource)

#### How to use
```swift
//
//  ViewController.swift
//  UITableViewDiffableDataSourceDemo
//
//  Created by Apple on 2022/3/11.
//

import UIKit

enum Section {
    case today
}

struct Note: Hashable {
    var idx: Int
    var content: String
}

class ViewController: UIViewController {
    private var dataSource: UITableViewDiffableDataSource<Section, Note>!

    private lazy var tableView: UITableView = {
        let tableView = UITableView()

        tableView.translatesAutoresizingMaskIntoConstraints = false
        tableView.register(UITableViewCell.self, forCellReuseIdentifier: String(describing: UITableViewCell.self))

        view.addSubview(tableView)

        return tableView
    }()

    override func loadView() {
        super.loadView()

        self.tableView.topAnchor.constraint(equalTo: self.view.topAnchor).isActive = true
        self.tableView.trailingAnchor.constraint(equalTo: self.view.trailingAnchor).isActive = true
        self.tableView.bottomAnchor.constraint(equalTo: self.view.bottomAnchor).isActive = true
        self.tableView.leadingAnchor.constraint(equalTo: self.view.leadingAnchor).isActive = true
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let noteList = [
            Note(idx: 0, content: "hello No.1"),
            Note(idx: 1, content: "hello No.2"),
            Note(idx: 2, content: "hello No.3"),
            Note(idx: 3, content: "hello No.4"),
            Note(idx: 4, content: "hello No.5"),
            Note(idx: 5, content: "hello No.6"),
        ]

        guard let data = getDataSource() else {
            return
        }

        self.dataSource = data
        self.updateData(noteList)
    }

    func getDataSource() -> UITableViewDiffableDataSource<Section, Note>? {
        UITableViewDiffableDataSource(tableView: self.tableView) { _, _, note in
            let cell = UITableViewCell()
            cell.textLabel?.text = note.content

            return cell
        }
    }

    func updateData(_ noteList: [Note]) {
        var snapshot = NSDiffableDataSourceSnapshot<Section, Note>()
        snapshot.appendSections([.today])
        snapshot.appendItems(noteList)

        self.dataSource.apply(snapshot, animatingDifferences: false, completion: nil)
    }
}

```