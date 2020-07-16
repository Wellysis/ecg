# iRhythm
## Dataset
<https://irhythm.github.io/cardiol_test_set>
* * *
## Code
- 파일명의 앞 부분을 이용하여 patient ID로 사용

        def patient_id(record):
            return os.path.basename(record).split("_")[0]

- 제공 받은 데이터의 한계로 함수 코드 변경, 결과는 실제 논문과 다를 수 있음.

        def load_train(data_path, dev_frac, epi_ext):
            records = get_all_records(data_path)
            train, dev = stratify(records, dev_frac)
            print("Constructing train...")
            train = construct_dataset(train, epi_ext)
            print("Constructing dev...")
            dev = construct_dataset(dev, epi_ext)

            reviewers = [load_rev_id(r, epi_ext) for r in records]
            train = [(e,l,r) for (e, l), r in zip(train, reviewers)]
            dev = [(e,l,r) for (e,l),r in zip(dev, reviewers)]

            return train, dev

- 다운받은 파일의 Data path 지정 (기존 blacklist와 관련된 path 변경)

        if __name__ == "__main__":
            data_dir = "path_to_dataset/CARDIOL_MAY_2017"
            blacklist_paths = ""
            data_path = os.path.abspath(data_dir)
            dev_frac = 0.2
            train, dev = load_train(data_path, dev_frac, '_grp*.episodes.json')
            make_json("train.json", train)
            make_json("dev.json", dev)
            test_dir = os.path.abspath(data_dir)
            test = load_test(test_dir, '_grp*.episodes.json')
            make_json("test.json", test)
            for i in range(6):
                test = load_test(test_dir, "_rev{}.episodes.json".format(i))
                make_json("test_rev{}.json".format(i), test)

* * *
## Training 
        python ecg/train.py examples/irhythm/config.json -e irhythm